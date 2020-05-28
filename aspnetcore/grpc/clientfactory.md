---
<span data-ttu-id="adb1a-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="adb1a-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="adb1a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="adb1a-102">'Blazor'</span></span>
- <span data-ttu-id="adb1a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="adb1a-103">'Identity'</span></span>
- <span data-ttu-id="adb1a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="adb1a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="adb1a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="adb1a-105">'Razor'</span></span>
- <span data-ttu-id="adb1a-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="adb1a-106">'SignalR' uid:</span></span> 

---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="adb1a-107">integrace klientské továrny gRPC v .NET Core</span><span class="sxs-lookup"><span data-stu-id="adb1a-107">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="adb1a-108">integrace gRPC s `HttpClientFactory` nabízí centralizovaný způsob vytváření klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="adb1a-108">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="adb1a-109">Dá se použít jako alternativa ke [konfiguraci samostatných klientských gRPC instancí](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="adb1a-109">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="adb1a-110">Integration Factory je k dispozici v balíčku NuGet [Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="adb1a-110">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="adb1a-111">Továrna nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="adb1a-111">The factory offers the following benefits:</span></span>

* <span data-ttu-id="adb1a-112">Poskytuje centrální umístění pro konfiguraci logických instancí klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="adb1a-112">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="adb1a-113">Spravuje životnost základního`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="adb1a-113">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="adb1a-114">Automatické šíření konečného termínu a zrušení ve službě ASP.NET Core gRPC</span><span class="sxs-lookup"><span data-stu-id="adb1a-114">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="adb1a-115">Registrace klientů gRPC</span><span class="sxs-lookup"><span data-stu-id="adb1a-115">Register gRPC clients</span></span>

<span data-ttu-id="adb1a-116">Pokud chcete zaregistrovat klienta gRPC, `AddGrpcClient` můžete použít obecnou metodu rozšíření v rámci `Startup.ConfigureServices` , zadáním klientské třídy gRPC typu a adresy služby:</span><span class="sxs-lookup"><span data-stu-id="adb1a-116">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="adb1a-117">Typ klienta gRPC je zaregistrován jako přechodný s vkládáním závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="adb1a-117">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="adb1a-118">Klienta se teď dá vložit a spotřebovat přímo v typech vytvořených pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="adb1a-118">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="adb1a-119">ASP.NET Core řadiče MVC, SignalR centra a gRPC služby jsou místa, kde můžou být automaticky vloženi klienti gRPC:</span><span class="sxs-lookup"><span data-stu-id="adb1a-119">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="adb1a-120">Konfigurace HttpClient</span><span class="sxs-lookup"><span data-stu-id="adb1a-120">Configure HttpClient</span></span>

<span data-ttu-id="adb1a-121">`HttpClientFactory`vytvoří klienta, který `HttpClient` používá klient gRPC.</span><span class="sxs-lookup"><span data-stu-id="adb1a-121">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="adb1a-122">Standardní `HttpClientFactory` metody lze použít pro přidání middlewaru pro odchozí žádosti nebo pro konfiguraci podkladu `HttpClientHandler` `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="adb1a-122">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="adb1a-123">Další informace najdete v tématu [Vytváření požadavků HTTP pomocí IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="adb1a-123">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="adb1a-124">Konfigurace kanálů a zachycení</span><span class="sxs-lookup"><span data-stu-id="adb1a-124">Configure Channel and Interceptors</span></span>

<span data-ttu-id="adb1a-125">k dispozici jsou metody specifické pro gRPC:</span><span class="sxs-lookup"><span data-stu-id="adb1a-125">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="adb1a-126">Konfigurace základního kanálu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="adb1a-126">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="adb1a-127">Přidejte `Interceptor` instance, které klient použije při volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="adb1a-127">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="adb1a-128">Termín a zrušení šíření</span><span class="sxs-lookup"><span data-stu-id="adb1a-128">Deadline and cancellation propagation</span></span>

<span data-ttu-id="adb1a-129">gRPC klienti, které vytvořila továrna ve službě gRPC, se dají nakonfigurovat `EnableCallContextPropagation()` tak, aby automaticky rozšířily konečný termín a token zrušení do podřízených volání.</span><span class="sxs-lookup"><span data-stu-id="adb1a-129">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="adb1a-130">`EnableCallContextPropagation()`Metoda rozšíření je k dispozici v balíčku NuGet [Grpc. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="adb1a-130">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="adb1a-131">Šíření kontextu volání funguje tak, že si přečtete konečný termín a token zrušení z aktuálního kontextu požadavku gRPC a automaticky je šíříte do odchozích volání prováděných klientem gRPC.</span><span class="sxs-lookup"><span data-stu-id="adb1a-131">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="adb1a-132">Šíření kontextu volání je vynikající způsob, jak zajistit, aby komplexní a vnořené gRPC scénáře vždy rozšířily konečný termín a zrušení.</span><span class="sxs-lookup"><span data-stu-id="adb1a-132">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="adb1a-133">Ve výchozím nastavení `EnableCallContextPropagation` vyvolá chybu, pokud se klient používá mimo kontext volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="adb1a-133">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="adb1a-134">Tato chyba je navržena tak, aby vás upozornila, že není k dispozici kontext volání pro rozšíření.</span><span class="sxs-lookup"><span data-stu-id="adb1a-134">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="adb1a-135">Pokud chcete použít klienta mimo kontext volání, potlačit chybu, pokud je klient konfigurován pomocí `SuppressContextNotFoundErrors` :</span><span class="sxs-lookup"><span data-stu-id="adb1a-135">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="adb1a-136">Další informace o termínech a zrušení vzdáleného volání procedur najdete v tématu [životní cyklus RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="adb1a-136">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="adb1a-137">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="adb1a-137">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
