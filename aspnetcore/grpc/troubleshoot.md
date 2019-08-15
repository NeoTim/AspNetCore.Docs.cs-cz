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
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="3a805-103">Řešení potíží s gRPC pro .NET Core</span><span class="sxs-lookup"><span data-stu-id="3a805-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="3a805-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3a805-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="3a805-105">Neshoda mezi konfigurací klienta a služby SSL/TLS</span><span class="sxs-lookup"><span data-stu-id="3a805-105">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="3a805-106">Šablona gRPC a ukázky používají protokol [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) k zabezpečení služeb gRPC ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="3a805-106">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="3a805-107">gRPC klienti musí používat zabezpečené připojení k úspěšnému volání zabezpečených služeb gRPC.</span><span class="sxs-lookup"><span data-stu-id="3a805-107">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="3a805-108">Můžete ověřit, že služba ASP.NET Core gRPC používá protokol TLS v protokolech zapsaných při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3a805-108">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="3a805-109">Služba bude naslouchat na koncovém bodu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3a805-109">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="3a805-110">Klient .NET Core musí v adrese `https` serveru použít k volání zabezpečeného připojení:</span><span class="sxs-lookup"><span data-stu-id="3a805-110">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

<span data-ttu-id="3a805-111">Všechny implementace klientů gRPC podporují protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="3a805-111">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="3a805-112">klienti gRPC z jiných jazyků obvykle vyžadují kanál konfigurovaný pomocí `SslCredentials`.</span><span class="sxs-lookup"><span data-stu-id="3a805-112">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="3a805-113">`SslCredentials`Určuje certifikát, který bude klient používat, a musí se používat místo nezabezpečených přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="3a805-113">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="3a805-114">Příklady konfigurace různých implementací klientů gRPC k použití protokolu TLS najdete v tématu [ověřování gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="3a805-114">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="3a805-115">Volání nezabezpečených služeb gRPC pomocí klienta .NET Core</span><span class="sxs-lookup"><span data-stu-id="3a805-115">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="3a805-116">Pro volání nezabezpečených služeb gRPC s klientem .NET Core je vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3a805-116">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="3a805-117">Klient gRPC musí na adrese serveru `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` nastavit přepínač `true` a použít `http` ho:</span><span class="sxs-lookup"><span data-stu-id="3a805-117">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="3a805-118">Nepovedlo se spustit aplikaci ASP.NET Core gRPC v macOS</span><span class="sxs-lookup"><span data-stu-id="3a805-118">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="3a805-119">Kestrel nepodporuje HTTP/2 s TLS v macOS a ve starších verzích Windows, jako je Windows 7.</span><span class="sxs-lookup"><span data-stu-id="3a805-119">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="3a805-120">ASP.NET Core šablona a ukázky gRPC standardně používají protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="3a805-120">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="3a805-121">Při pokusu o spuštění serveru gRPC se zobrazí následující chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="3a805-121">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="3a805-122">Nedá se vytvořit vazba https://localhost:5001 na rozhraní zpětné smyčky IPv4: HTTP/2 přes TLS se v macOS nepodporuje kvůli chybějící podpoře ALPN.</span><span class="sxs-lookup"><span data-stu-id="3a805-122">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="3a805-123">Pokud chcete tento problém obejít, nakonfigurujte Kestrel a klienta gRPC na použití HTTP/2 *bez* TLS.</span><span class="sxs-lookup"><span data-stu-id="3a805-123">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="3a805-124">To byste měli udělat jenom během vývoje.</span><span class="sxs-lookup"><span data-stu-id="3a805-124">You should only do this during development.</span></span> <span data-ttu-id="3a805-125">Nepoužíváte-li protokol TLS, budou zasílány zprávy gRPC bez šifrování.</span><span class="sxs-lookup"><span data-stu-id="3a805-125">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="3a805-126">Kestrel musí Konfigurovat koncový bod HTTP/2 bez TLS v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3a805-126">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="3a805-127">Klient gRPC musí být taky nakonfigurovaný tak, aby nepoužíval protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="3a805-127">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="3a805-128">Další informace najdete v tématu [Calling The gRPC Services with a .NET Core Client](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="3a805-128">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="3a805-129">HTTP/2 bez protokolu TLS by se mělo používat jenom při vývoji aplikací.</span><span class="sxs-lookup"><span data-stu-id="3a805-129">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="3a805-130">Provozní aplikace by měly vždycky používat zabezpečení přenosu.</span><span class="sxs-lookup"><span data-stu-id="3a805-130">Production apps should always use transport security.</span></span> <span data-ttu-id="3a805-131">Další informace najdete v tématu věnovaném [hlediskům zabezpečení v gRPC pro ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="3a805-131">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="3a805-132">prostředky C# gRPC nejsou generovány z  *\*kódu. proto* soubory</span><span class="sxs-lookup"><span data-stu-id="3a805-132">gRPC C# assets are not code generated from *\*.proto* files</span></span>

<span data-ttu-id="3a805-133">generování kódu gRPC konkrétní klienti a základní třídy služby vyžaduje soubory protobuf a nástroje, na které se odkazuje z projektu.</span><span class="sxs-lookup"><span data-stu-id="3a805-133">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="3a805-134">Musíte zahrnout:</span><span class="sxs-lookup"><span data-stu-id="3a805-134">You must include:</span></span>

* <span data-ttu-id="3a805-135">*. proto* soubory, které chcete použít ve `<Protobuf>` skupině položek.</span><span class="sxs-lookup"><span data-stu-id="3a805-135">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="3a805-136">[Importováno *. proto* ](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) musí být na soubory odkazováno v projektu.</span><span class="sxs-lookup"><span data-stu-id="3a805-136">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="3a805-137">Odkaz na balíček nástrojů pro gRPC nástrojů pro [gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="3a805-137">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="3a805-138">Další informace o generování prostředků gRPC C# naleznete v tématu <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="3a805-138">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="3a805-139">Ve výchozím nastavení `<Protobuf>` odkaz vygeneruje konkrétního klienta a základní třídu služby.</span><span class="sxs-lookup"><span data-stu-id="3a805-139">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="3a805-140">`GrpcServices` Atribut referenčního prvku lze použít k omezení C# generování prostředků.</span><span class="sxs-lookup"><span data-stu-id="3a805-140">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="3a805-141">Platné `GrpcServices` možnosti jsou:</span><span class="sxs-lookup"><span data-stu-id="3a805-141">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="3a805-142">`Both`(výchozí, není-li k dispozici)</span><span class="sxs-lookup"><span data-stu-id="3a805-142">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="3a805-143">ASP.NET Core webová aplikace hostující služby gRPC potřebuje jenom vygenerovanou základní třídu služby:</span><span class="sxs-lookup"><span data-stu-id="3a805-143">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="3a805-144">Klientská aplikace gRPC, která provádí volání gRPC, potřebuje jenom konkrétního vygenerovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="3a805-144">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
