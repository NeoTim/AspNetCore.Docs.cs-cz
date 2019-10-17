---
title: Řešení potíží s gRPC pro .NET Core
author: jamesnk
description: Řešení chyb při použití gRPC v .NET Core
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519052"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="9a85a-103">Řešení potíží s gRPC pro .NET Core</span><span class="sxs-lookup"><span data-stu-id="9a85a-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="9a85a-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9a85a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="9a85a-105">Tento dokument popisuje běžně zjištěné problémy při vývoji aplikací gRPC na platformě .NET.</span><span class="sxs-lookup"><span data-stu-id="9a85a-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="9a85a-106">Neshoda mezi konfigurací klienta a služby SSL/TLS</span><span class="sxs-lookup"><span data-stu-id="9a85a-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="9a85a-107">Šablona gRPC a ukázky používají protokol [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) k zabezpečení služeb gRPC ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="9a85a-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="9a85a-108">gRPC klienti musí používat zabezpečené připojení k úspěšnému volání zabezpečených služeb gRPC.</span><span class="sxs-lookup"><span data-stu-id="9a85a-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="9a85a-109">Můžete ověřit, že služba ASP.NET Core gRPC používá protokol TLS v protokolech zapsaných při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a85a-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="9a85a-110">Služba bude naslouchat na koncovém bodu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="9a85a-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="9a85a-111">Klient .NET Core musí v adrese serveru použít `https`, aby bylo možné volat pomocí zabezpečeného připojení:</span><span class="sxs-lookup"><span data-stu-id="9a85a-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="9a85a-112">Všechny implementace klientů gRPC podporují protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="9a85a-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="9a85a-113">gRPC klienti z jiných jazyků obvykle vyžadují kanál nakonfigurovaný pomocí `SslCredentials`.</span><span class="sxs-lookup"><span data-stu-id="9a85a-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="9a85a-114">`SslCredentials` Určuje certifikát, který bude klient používat, a musí se používat místo nezabezpečených přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="9a85a-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="9a85a-115">Příklady konfigurace různých implementací klientů gRPC k použití protokolu TLS najdete v tématu [ověřování gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="9a85a-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="9a85a-116">Volání služby gRPC s nedůvěryhodným/neplatným certifikátem</span><span class="sxs-lookup"><span data-stu-id="9a85a-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="9a85a-117">Klient .NET gRPC vyžaduje, aby služba měla důvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="9a85a-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="9a85a-118">Při volání služby gRPC bez důvěryhodného certifikátu se vrátí následující chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="9a85a-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="9a85a-119">Neošetřená výjimka.</span><span class="sxs-lookup"><span data-stu-id="9a85a-119">Unhandled exception.</span></span> <span data-ttu-id="9a85a-120">System .NET. http. HttpRequestException: nepovedlo se navázat připojení SSL, viz vnitřní výjimka.</span><span class="sxs-lookup"><span data-stu-id="9a85a-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="9a85a-121">---> System. Security. Authentication. AuthenticationException –: vzdálený certifikát je podle ověřovací procedury neplatný.</span><span class="sxs-lookup"><span data-stu-id="9a85a-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="9a85a-122">Tato chyba se může zobrazit, pokud testujete aplikaci místně a ASP.NET Core certifikát pro vývoj HTTPS není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="9a85a-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="9a85a-123">Pokyny k vyřešení tohoto problému naleznete v tématu [Trust ASP.NET Core certifikát pro vývoj https ve Windows a MacOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="9a85a-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="9a85a-124">Pokud voláte službu gRPC na jiném počítači a nemůžete důvěřovat certifikátu, může být klient gRPC nakonfigurovaný tak, aby ignoroval neplatný certifikát.</span><span class="sxs-lookup"><span data-stu-id="9a85a-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="9a85a-125">Následující kód používá [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) k povolení volání bez důvěryhodného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="9a85a-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

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
> <span data-ttu-id="9a85a-126">Nedůvěryhodné certifikáty by se měly používat jenom při vývoji aplikací.</span><span class="sxs-lookup"><span data-stu-id="9a85a-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="9a85a-127">Produkční aplikace by měly vždy používat platné certifikáty.</span><span class="sxs-lookup"><span data-stu-id="9a85a-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="9a85a-128">Volání nezabezpečených služeb gRPC pomocí klienta .NET Core</span><span class="sxs-lookup"><span data-stu-id="9a85a-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="9a85a-129">Pro volání nezabezpečených služeb gRPC s klientem .NET Core je vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9a85a-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="9a85a-130">Klient gRPC musí nastavit přepínač `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` na `true` a použít `http` na adrese serveru:</span><span class="sxs-lookup"><span data-stu-id="9a85a-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="9a85a-131">Nepovedlo se spustit aplikaci ASP.NET Core gRPC v macOS</span><span class="sxs-lookup"><span data-stu-id="9a85a-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="9a85a-132">Kestrel nepodporuje HTTP/2 s TLS v macOS a ve starších verzích Windows, jako je Windows 7.</span><span class="sxs-lookup"><span data-stu-id="9a85a-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="9a85a-133">ASP.NET Core šablona a ukázky gRPC standardně používají protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="9a85a-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="9a85a-134">Při pokusu o spuštění serveru gRPC se zobrazí následující chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="9a85a-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="9a85a-135">Nepovedlo se vytvořit připojení k https://localhost:5001 na rozhraní zpětné smyčky IPv4: HTTP/2 přes TLS se v macOS nepodporuje kvůli chybějící podpoře ALPN.</span><span class="sxs-lookup"><span data-stu-id="9a85a-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="9a85a-136">Pokud chcete tento problém obejít, nakonfigurujte Kestrel a klienta gRPC na použití HTTP/2 *bez* TLS.</span><span class="sxs-lookup"><span data-stu-id="9a85a-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="9a85a-137">To byste měli udělat jenom během vývoje.</span><span class="sxs-lookup"><span data-stu-id="9a85a-137">You should only do this during development.</span></span> <span data-ttu-id="9a85a-138">Nepoužíváte-li protokol TLS, budou zasílány zprávy gRPC bez šifrování.</span><span class="sxs-lookup"><span data-stu-id="9a85a-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="9a85a-139">Kestrel musí Konfigurovat koncový bod HTTP/2 bez TLS v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a85a-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="9a85a-140">Pokud je koncový bod HTTP/2 nakonfigurovaný bez TLS, musí být [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) koncového bodu nastavené na `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="9a85a-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="9a85a-141">`HttpProtocols.Http1AndHttp2` nelze použít, protože pro vyjednání HTTP/2 je vyžadován protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="9a85a-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="9a85a-142">Bez TLS budou všechna připojení ke koncovému bodu ve výchozím nastavení HTTP/1.1 a volání gRPC neúspěšná.</span><span class="sxs-lookup"><span data-stu-id="9a85a-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="9a85a-143">Klient gRPC musí být taky nakonfigurovaný tak, aby nepoužíval protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="9a85a-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="9a85a-144">Další informace najdete v tématu [Calling The gRPC Services with a .NET Core Client](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="9a85a-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="9a85a-145">HTTP/2 bez protokolu TLS by se mělo používat jenom při vývoji aplikací.</span><span class="sxs-lookup"><span data-stu-id="9a85a-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="9a85a-146">Provozní aplikace by měly vždycky používat zabezpečení přenosu.</span><span class="sxs-lookup"><span data-stu-id="9a85a-146">Production apps should always use transport security.</span></span> <span data-ttu-id="9a85a-147">Další informace najdete v tématu věnovaném [hlediskům zabezpečení v gRPC pro ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="9a85a-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="9a85a-148">prostředky C# gRPC nejsou generovány z kódu. Proto soubory</span><span class="sxs-lookup"><span data-stu-id="9a85a-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="9a85a-149">generování kódu gRPC konkrétní klienti a základní třídy služby vyžaduje soubory protobuf a nástroje, na které se odkazuje z projektu.</span><span class="sxs-lookup"><span data-stu-id="9a85a-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="9a85a-150">Musíte zahrnout:</span><span class="sxs-lookup"><span data-stu-id="9a85a-150">You must include:</span></span>

* <span data-ttu-id="9a85a-151">*. proto* soubory, které chcete použít ve skupině položek `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="9a85a-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="9a85a-152">[Importováno *. proto* ](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) musí být na soubory odkazováno v projektu.</span><span class="sxs-lookup"><span data-stu-id="9a85a-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="9a85a-153">Odkaz na balíček nástrojů pro gRPC nástrojů pro [gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="9a85a-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="9a85a-154">Další informace o generování prostředků gRPC C# naleznete v tématu <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="9a85a-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="9a85a-155">Ve výchozím nastavení vygeneruje odkaz `<Protobuf>` konkrétního klienta a základní třídu služby.</span><span class="sxs-lookup"><span data-stu-id="9a85a-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="9a85a-156">Pomocí atributu `GrpcServices` elementu reference lze omezit C# generování prostředků.</span><span class="sxs-lookup"><span data-stu-id="9a85a-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="9a85a-157">Platné možnosti `GrpcServices` jsou následující:</span><span class="sxs-lookup"><span data-stu-id="9a85a-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="9a85a-158">`Both` (výchozí, není-li k dispozici)</span><span class="sxs-lookup"><span data-stu-id="9a85a-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="9a85a-159">ASP.NET Core webová aplikace hostující služby gRPC potřebuje jenom vygenerovanou základní třídu služby:</span><span class="sxs-lookup"><span data-stu-id="9a85a-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="9a85a-160">Klientská aplikace gRPC, která provádí volání gRPC, potřebuje jenom konkrétního vygenerovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="9a85a-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="9a85a-161">Projekty WPF nemůžou generovat gRPC C# prostředky z. Proto soubory</span><span class="sxs-lookup"><span data-stu-id="9a85a-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="9a85a-162">Projekty WPF mají [známý problém](https://github.com/dotnet/wpf/issues/810) , který brání správnému fungování generování kódu gRPC.</span><span class="sxs-lookup"><span data-stu-id="9a85a-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="9a85a-163">Všechny typy gRPC generované v projektu WPF odkazem na `Grpc.Tools` a *. proto* soubory vytvoří chyby kompilace při použití:</span><span class="sxs-lookup"><span data-stu-id="9a85a-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="9a85a-164">Chyba CS0246: nepovedlo se najít typ nebo název oboru názvů ' MyGrpcServices ' (nechybí Direktiva using nebo odkaz na sestavení?)</span><span class="sxs-lookup"><span data-stu-id="9a85a-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="9a85a-165">Tento problém můžete vyřešit pomocí těchto potíží:</span><span class="sxs-lookup"><span data-stu-id="9a85a-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="9a85a-166">Vytvořte nový projekt knihovny tříd .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a85a-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="9a85a-167">V novém projektu přidejte odkazy pro povolení [ C# generování kódu z *@no__t -3.* a soubory](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="9a85a-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="9a85a-168">Přidejte odkaz na balíček do balíčku [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="9a85a-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="9a85a-169">Přidejte *@no__t -1.* Files do skupiny položek `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="9a85a-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="9a85a-170">V aplikaci WPF přidejte odkaz na nový projekt.</span><span class="sxs-lookup"><span data-stu-id="9a85a-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="9a85a-171">Aplikace WPF může použít gRPC generované typy z nového projektu knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="9a85a-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
