---
title: Řešení potíží s gRPC pro .NET Core
author: jamesnk
description: Řešení chyb při použití gRPC v .NET Core
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/troubleshoot
ms.openlocfilehash: 33864ceb18304eb1d3413bcc9aebacd6eaffdbc6
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878503"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="b3eab-103">Řešení potíží s gRPC pro .NET Core</span><span class="sxs-lookup"><span data-stu-id="b3eab-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="b3eab-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="b3eab-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="b3eab-105">Tento dokument popisuje běžně zjištěné problémy při vývoji aplikací gRPC na platformě .NET.</span><span class="sxs-lookup"><span data-stu-id="b3eab-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="b3eab-106">Neshoda mezi konfigurací klienta a služby SSL/TLS</span><span class="sxs-lookup"><span data-stu-id="b3eab-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="b3eab-107">Šablona gRPC a ukázky používají protokol [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) k zabezpečení služeb gRPC ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="b3eab-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="b3eab-108">gRPC klienti musí používat zabezpečené připojení k úspěšnému volání zabezpečených služeb gRPC.</span><span class="sxs-lookup"><span data-stu-id="b3eab-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="b3eab-109">Můžete ověřit, že služba ASP.NET Core gRPC používá protokol TLS v protokolech zapsaných při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b3eab-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="b3eab-110">Služba bude naslouchat na koncovém bodu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="b3eab-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="b3eab-111">Klient .NET Core musí v adrese `https` serveru použít k volání zabezpečeného připojení:</span><span class="sxs-lookup"><span data-stu-id="b3eab-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="b3eab-112">Všechny implementace klientů gRPC podporují protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="b3eab-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="b3eab-113">klienti gRPC z jiných jazyků obvykle vyžadují kanál konfigurovaný pomocí `SslCredentials`.</span><span class="sxs-lookup"><span data-stu-id="b3eab-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="b3eab-114">`SslCredentials`Určuje certifikát, který bude klient používat, a musí se používat místo nezabezpečených přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="b3eab-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="b3eab-115">Příklady konfigurace různých implementací klientů gRPC k použití protokolu TLS najdete v tématu [ověřování gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="b3eab-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="b3eab-116">Volání služby gRPC s nedůvěryhodným/neplatným certifikátem</span><span class="sxs-lookup"><span data-stu-id="b3eab-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="b3eab-117">Klient .NET gRPC vyžaduje, aby služba měla důvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="b3eab-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="b3eab-118">Při volání služby gRPC bez důvěryhodného certifikátu se vrátí následující chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="b3eab-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="b3eab-119">Neošetřená výjimka.</span><span class="sxs-lookup"><span data-stu-id="b3eab-119">Unhandled exception.</span></span> <span data-ttu-id="b3eab-120">System.Net.Http.HttpRequestException: Nebylo možné navázat připojení SSL, viz vnitřní výjimka.</span><span class="sxs-lookup"><span data-stu-id="b3eab-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="b3eab-121">---> System. Security. Authentication. AuthenticationException –: Vzdálený certifikát je podle ověřovací procedury neplatný.</span><span class="sxs-lookup"><span data-stu-id="b3eab-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="b3eab-122">Tato chyba se může zobrazit, pokud testujete aplikaci místně a ASP.NET Core certifikát pro vývoj HTTPS není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="b3eab-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="b3eab-123">Pokyny k vyřešení tohoto problému naleznete v tématu [Trust ASP.NET Core certifikát pro vývoj https ve Windows a MacOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="b3eab-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="b3eab-124">Pokud voláte službu gRPC na jiném počítači a nemůžete důvěřovat certifikátu, může být klient gRPC nakonfigurovaný tak, aby ignoroval neplatný certifikát.</span><span class="sxs-lookup"><span data-stu-id="b3eab-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="b3eab-125">Následující kód používá [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) k povolení volání bez důvěryhodného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="b3eab-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) => true;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="b3eab-126">Nedůvěryhodné certifikáty by se měly používat jenom při vývoji aplikací.</span><span class="sxs-lookup"><span data-stu-id="b3eab-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="b3eab-127">Produkční aplikace by měly vždy používat platné certifikáty.</span><span class="sxs-lookup"><span data-stu-id="b3eab-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="b3eab-128">Volání nezabezpečených služeb gRPC pomocí klienta .NET Core</span><span class="sxs-lookup"><span data-stu-id="b3eab-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="b3eab-129">Pro volání nezabezpečených služeb gRPC s klientem .NET Core je vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b3eab-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="b3eab-130">Klient gRPC musí na adrese serveru `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` nastavit přepínač `true` a použít `http` ho:</span><span class="sxs-lookup"><span data-stu-id="b3eab-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="b3eab-131">Nepovedlo se spustit aplikaci ASP.NET Core gRPC v macOS</span><span class="sxs-lookup"><span data-stu-id="b3eab-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="b3eab-132">Kestrel nepodporuje HTTP/2 s TLS v macOS a ve starších verzích Windows, jako je Windows 7.</span><span class="sxs-lookup"><span data-stu-id="b3eab-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="b3eab-133">ASP.NET Core šablona a ukázky gRPC standardně používají protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="b3eab-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="b3eab-134">Při pokusu o spuštění serveru gRPC se zobrazí následující chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="b3eab-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="b3eab-135">Nedá se vytvořit vazba https://localhost:5001 na rozhraní zpětné smyčky IPv4: HTTP/2 přes TLS se v macOS nepodporuje kvůli chybějící podpoře ALPN.</span><span class="sxs-lookup"><span data-stu-id="b3eab-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="b3eab-136">Pokud chcete tento problém obejít, nakonfigurujte Kestrel a klienta gRPC na použití HTTP/2 *bez* TLS.</span><span class="sxs-lookup"><span data-stu-id="b3eab-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="b3eab-137">To byste měli udělat jenom během vývoje.</span><span class="sxs-lookup"><span data-stu-id="b3eab-137">You should only do this during development.</span></span> <span data-ttu-id="b3eab-138">Nepoužíváte-li protokol TLS, budou zasílány zprávy gRPC bez šifrování.</span><span class="sxs-lookup"><span data-stu-id="b3eab-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="b3eab-139">Kestrel musí Konfigurovat koncový bod HTTP/2 bez TLS v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b3eab-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="b3eab-140">Pokud je koncový bod HTTP/2 nakonfigurovaný bez TLS, musí být [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) koncového bodu nastaven `HttpProtocols.Http2`na.</span><span class="sxs-lookup"><span data-stu-id="b3eab-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="b3eab-141">`HttpProtocols.Http1AndHttp2`nelze použít, protože pro vyjednání HTTP/2 je vyžadován protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="b3eab-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="b3eab-142">Bez TLS budou všechna připojení ke koncovému bodu ve výchozím nastavení HTTP/1.1 a volání gRPC neúspěšná.</span><span class="sxs-lookup"><span data-stu-id="b3eab-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="b3eab-143">Klient gRPC musí být taky nakonfigurovaný tak, aby nepoužíval protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="b3eab-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="b3eab-144">Další informace najdete v tématu [Calling The gRPC Services with a .NET Core Client](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="b3eab-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="b3eab-145">HTTP/2 bez protokolu TLS by se mělo používat jenom při vývoji aplikací.</span><span class="sxs-lookup"><span data-stu-id="b3eab-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="b3eab-146">Provozní aplikace by měly vždycky používat zabezpečení přenosu.</span><span class="sxs-lookup"><span data-stu-id="b3eab-146">Production apps should always use transport security.</span></span> <span data-ttu-id="b3eab-147">Další informace najdete v tématu věnovaném [hlediskům zabezpečení v gRPC pro ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="b3eab-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="b3eab-148">prostředky C# gRPC nejsou generovány z  *\*kódu. proto* soubory</span><span class="sxs-lookup"><span data-stu-id="b3eab-148">gRPC C# assets are not code generated from *\*.proto* files</span></span>

<span data-ttu-id="b3eab-149">generování kódu gRPC konkrétní klienti a základní třídy služby vyžaduje soubory protobuf a nástroje, na které se odkazuje z projektu.</span><span class="sxs-lookup"><span data-stu-id="b3eab-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="b3eab-150">Musíte zahrnout:</span><span class="sxs-lookup"><span data-stu-id="b3eab-150">You must include:</span></span>

* <span data-ttu-id="b3eab-151">*. proto* soubory, které chcete použít ve `<Protobuf>` skupině položek.</span><span class="sxs-lookup"><span data-stu-id="b3eab-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="b3eab-152">[Importováno *. proto* ](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) musí být na soubory odkazováno v projektu.</span><span class="sxs-lookup"><span data-stu-id="b3eab-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="b3eab-153">Odkaz na balíček nástrojů pro gRPC nástrojů pro [gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="b3eab-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="b3eab-154">Další informace o generování prostředků gRPC C# naleznete v tématu <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="b3eab-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="b3eab-155">Ve výchozím nastavení `<Protobuf>` odkaz vygeneruje konkrétního klienta a základní třídu služby.</span><span class="sxs-lookup"><span data-stu-id="b3eab-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="b3eab-156">`GrpcServices` Atribut referenčního prvku lze použít k omezení C# generování prostředků.</span><span class="sxs-lookup"><span data-stu-id="b3eab-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="b3eab-157">Platné `GrpcServices` možnosti jsou:</span><span class="sxs-lookup"><span data-stu-id="b3eab-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="b3eab-158">`Both`(výchozí, není-li k dispozici)</span><span class="sxs-lookup"><span data-stu-id="b3eab-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="b3eab-159">ASP.NET Core webová aplikace hostující služby gRPC potřebuje jenom vygenerovanou základní třídu služby:</span><span class="sxs-lookup"><span data-stu-id="b3eab-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="b3eab-160">Klientská aplikace gRPC, která provádí volání gRPC, potřebuje jenom konkrétního vygenerovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="b3eab-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
