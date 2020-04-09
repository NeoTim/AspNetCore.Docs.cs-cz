---
title: Poradce při potížích s gRPC v jádru rozhraní .NET
author: jamesnk
description: Poradce při potížích při použití gRPC na .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664127"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="db96c-103">Poradce při potížích s gRPC v jádru rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="db96c-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="db96c-104">Podle [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="db96c-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="db96c-105">Tento dokument popisuje běžně se vyskytující problémy při vývoji gRPC aplikací na rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="db96c-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="db96c-106">Neshoda mezi konfigurací SSL/TLS klienta a služby</span><span class="sxs-lookup"><span data-stu-id="db96c-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="db96c-107">Šablona gRPC a ukázky používají [zabezpečení transportní vrstvy (TLS)](https://tools.ietf.org/html/rfc5246) k zabezpečení služeb gRPC ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="db96c-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="db96c-108">gRPC klienti musí k úspěšnému volání zabezpečených služeb gRPC používat zabezpečené připojení.</span><span class="sxs-lookup"><span data-stu-id="db96c-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="db96c-109">Můžete ověřit, ASP.NET služba Core gRPC používá TLS v protokolech napsaných při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="db96c-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="db96c-110">Služba bude poslouchat na koncovém bodu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="db96c-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="db96c-111">Klient .NET Core `https` musí použít v adrese serveru volání se zabezpečeným připojením:</span><span class="sxs-lookup"><span data-stu-id="db96c-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="db96c-112">Všechny implementace klientů gRPC podporují TLS.</span><span class="sxs-lookup"><span data-stu-id="db96c-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="db96c-113">klienti gRPC z jiných jazyků obvykle `SslCredentials`vyžadují kanál nakonfigurovaný pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="db96c-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="db96c-114">`SslCredentials`určuje certifikát, který bude klient používat, a musí být použit namísto nezabezpečených pověření.</span><span class="sxs-lookup"><span data-stu-id="db96c-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="db96c-115">Příklady konfigurace různých implementací klienta gRPC pro použití Protokolu TLS naleznete [v tématu gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="db96c-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="db96c-116">Volání služby gRPC s nedůvěryhodným/neplatným certifikátem</span><span class="sxs-lookup"><span data-stu-id="db96c-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="db96c-117">Klient gRPC rozhraní vyžaduje, aby služba měla důvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="db96c-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="db96c-118">Při volání služby gRPC bez důvěryhodného certifikátu je vrácena následující chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="db96c-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="db96c-119">Neošetřená výjimka.</span><span class="sxs-lookup"><span data-stu-id="db96c-119">Unhandled exception.</span></span> <span data-ttu-id="db96c-120">System.Net.Http.HttpRequestException: Připojení SSL nelze navázat, viz vnitřní výjimka.</span><span class="sxs-lookup"><span data-stu-id="db96c-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="db96c-121">---> System.Security.Authentication.AuthenticationException: Vzdálený certifikát je podle ověřovacího postupu neplatný.</span><span class="sxs-lookup"><span data-stu-id="db96c-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="db96c-122">Tato chyba se může zobrazit, pokud testujete aplikaci místně a ASP.NET vývojový certifikát Core HTTPS není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="db96c-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="db96c-123">Pokyny k vyřešení tohoto problému naleznete [v tématu Trust ASP.NET core https development certificate ve Windows a macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="db96c-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="db96c-124">Pokud voláte službu gRPC v jiném počítači a nemůžete certifikátu důvěřovat, pak lze klienta gRPC nakonfigurovat tak, aby ignoroval neplatný certifikát.</span><span class="sxs-lookup"><span data-stu-id="db96c-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="db96c-125">Následující kód používá [httpClientHandler.ServerCertificateValidationValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) k povolení volání bez důvěryhodného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="db96c-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

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
> <span data-ttu-id="db96c-126">Nedůvěryhodné certifikáty by se měly používat pouze během vývoje aplikací.</span><span class="sxs-lookup"><span data-stu-id="db96c-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="db96c-127">Produkční aplikace by měly vždy používat platné certifikáty.</span><span class="sxs-lookup"><span data-stu-id="db96c-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="db96c-128">Volání nezabezpečených služeb gRPC s klientem .NET Core</span><span class="sxs-lookup"><span data-stu-id="db96c-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="db96c-129">Další konfigurace je vyžadována pro volání nezabezpečené služby gRPC s klientem .NET Core.</span><span class="sxs-lookup"><span data-stu-id="db96c-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="db96c-130">Klient gRPC musí `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` nastavit `true` přepínač `http` a použít v adrese serveru:</span><span class="sxs-lookup"><span data-stu-id="db96c-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="db96c-131">Nelze spustit aplikaci core gRPC ASP.NET v systému macOS</span><span class="sxs-lookup"><span data-stu-id="db96c-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="db96c-132">Kestrel nepodporuje HTTP/2 s TLS v systému macOS a starších verzích systému Windows, jako je Windows 7.</span><span class="sxs-lookup"><span data-stu-id="db96c-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="db96c-133">Šablona ASP.NET Core gRPC a ukázky ve výchozím nastavení používají TLS.</span><span class="sxs-lookup"><span data-stu-id="db96c-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="db96c-134">Při pokusu o spuštění serveru gRPC se zobrazí následující chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="db96c-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="db96c-135">Nelze vytvořit https://localhost:5001 vazbu na rozhraní zpětné smyčky IPv4: "HTTP/2 přes TLS není v systému macOS podporován z důvodu chybějící podpory ALPN.'.</span><span class="sxs-lookup"><span data-stu-id="db96c-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="db96c-136">Chcete-li tento problém vyřešit, nakonfigurujte Kestrel a klientgRPC používat HTTP/2 *bez* TLS.</span><span class="sxs-lookup"><span data-stu-id="db96c-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="db96c-137">Měli byste to provést pouze během vývoje.</span><span class="sxs-lookup"><span data-stu-id="db96c-137">You should only do this during development.</span></span> <span data-ttu-id="db96c-138">Nepoužívání TLS bude mít za následek gRPC zprávy odesílané bez šifrování.</span><span class="sxs-lookup"><span data-stu-id="db96c-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="db96c-139">Poštolka musí nakonfigurovat koncový bod HTTP/2 bez TLS v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="db96c-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="db96c-140">Pokud je koncový bod HTTP/2 nakonfigurován bez protokolu TLS, musí být `HttpProtocols.Http2`protokol [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) koncového bodu nastaven na .</span><span class="sxs-lookup"><span data-stu-id="db96c-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="db96c-141">`HttpProtocols.Http1AndHttp2`nelze použít, protože tls je nutné vyjednat HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="db96c-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="db96c-142">Bez TLS všechna připojení ke koncovému bodu výchozí HTTP/1.1 a gRPC volání nezdaří.</span><span class="sxs-lookup"><span data-stu-id="db96c-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="db96c-143">Klient gRPC musí být také nakonfigurován tak, aby nepoužíval TLS.</span><span class="sxs-lookup"><span data-stu-id="db96c-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="db96c-144">Další informace naleznete [v tématu Volání nezabezpečených služeb gRPC s klientem .NET Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="db96c-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="db96c-145">HTTP/2 bez TLS by se měl používat pouze během vývoje aplikace.</span><span class="sxs-lookup"><span data-stu-id="db96c-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="db96c-146">Produkční aplikace by měly vždy používat zabezpečení přenosu.</span><span class="sxs-lookup"><span data-stu-id="db96c-146">Production apps should always use transport security.</span></span> <span data-ttu-id="db96c-147">Další informace naleznete [v tématu Důležité informace o zabezpečení v gRPC pro ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="db96c-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="db96c-148">GRPC C# assets are not code generated from .proto files gRPC C# assets are not code generated from .proto files gRPC C# assets are not code generated from .proto files gR</span><span class="sxs-lookup"><span data-stu-id="db96c-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="db96c-149">gRPC generování kódu konkrétních klientů a základní třídy služeb vyžaduje protobuf soubory a nástroje, které mají být odkazovány z projektu.</span><span class="sxs-lookup"><span data-stu-id="db96c-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="db96c-150">Musíte uvést:</span><span class="sxs-lookup"><span data-stu-id="db96c-150">You must include:</span></span>

* <span data-ttu-id="db96c-151">*.proto* soubory, které chcete `<Protobuf>` použít ve skupině položek.</span><span class="sxs-lookup"><span data-stu-id="db96c-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="db96c-152">[Importované soubory *.proto* ](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) musí být projektem odkazovány.</span><span class="sxs-lookup"><span data-stu-id="db96c-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="db96c-153">Odkaz na balíček gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="db96c-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="db96c-154">Další informace o generování prostředků gRPC <xref:grpc/basics>C# naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="db96c-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="db96c-155">Ve výchozím `<Protobuf>` nastavení odkaz generuje konkrétníklienta a základní třídu služby.</span><span class="sxs-lookup"><span data-stu-id="db96c-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="db96c-156">Atribut referenčního `GrpcServices` prvku lze použít k omezení generování majetku jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="db96c-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="db96c-157">Platné `GrpcServices` možnosti jsou:</span><span class="sxs-lookup"><span data-stu-id="db96c-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="db96c-158">`Both`(výchozí, pokud není přítomen)</span><span class="sxs-lookup"><span data-stu-id="db96c-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="db96c-159">ASP.NET Základní webová aplikace hostující služby gRPC potřebuje pouze vygenerovanou třídu služby:</span><span class="sxs-lookup"><span data-stu-id="db96c-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="db96c-160">Klientská aplikace gRPC, která provádí volání gRPC, potřebuje pouze konkrétního klienta generovaného:</span><span class="sxs-lookup"><span data-stu-id="db96c-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="db96c-161">WPF projekty nelze generovat gRPC C# prostředky ze souborů .proto</span><span class="sxs-lookup"><span data-stu-id="db96c-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="db96c-162">WPF projekty mají [známý problém,](https://github.com/dotnet/wpf/issues/810) který brání generování kódu gRPC pracovat správně.</span><span class="sxs-lookup"><span data-stu-id="db96c-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="db96c-163">Všechny typy gRPC generované v projektu WPF odkazem `Grpc.Tools` a *.proto* soubory vytvoří chyby kompilace při použití:</span><span class="sxs-lookup"><span data-stu-id="db96c-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="db96c-164">chyba CS0246: Typ nebo název oboru názvů MyGrpcServices nebyl nalezen (chybí vám direktiva using nebo odkaz na sestavení?)</span><span class="sxs-lookup"><span data-stu-id="db96c-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="db96c-165">Tento problém můžete vyřešit takto:</span><span class="sxs-lookup"><span data-stu-id="db96c-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="db96c-166">Vytvořte nový projekt knihovny tříd .NET Core.</span><span class="sxs-lookup"><span data-stu-id="db96c-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="db96c-167">V novém projektu přidejte odkazy, které umožní [generování kódu Jazyka C# ze \* \*\* souborů .proto](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="db96c-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="db96c-168">Přidejte odkaz na balíček do balíčku [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)</span><span class="sxs-lookup"><span data-stu-id="db96c-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="db96c-169">Přidejte \* \*\* soubory .proto do skupiny `<Protobuf>` položek.</span><span class="sxs-lookup"><span data-stu-id="db96c-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="db96c-170">V aplikaci WPF přidejte odkaz na nový projekt.</span><span class="sxs-lookup"><span data-stu-id="db96c-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="db96c-171">Aplikace WPF můžete použít gRPC generované typy z projektu nové knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="db96c-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
