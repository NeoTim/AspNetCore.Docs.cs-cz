---
title: Řešení potíží s gRPC pro .NET Core
author: jamesnk
description: Řešení chyb při použití gRPC v .NET Core
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/troubleshoot
ms.openlocfilehash: 385291ec6bb6719a5fade927fa9f599af8c94045
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176176"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="24f24-103">Řešení potíží s gRPC pro .NET Core</span><span class="sxs-lookup"><span data-stu-id="24f24-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="24f24-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="24f24-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="24f24-105">Tento dokument popisuje běžně zjištěné problémy při vývoji aplikací gRPC na platformě .NET.</span><span class="sxs-lookup"><span data-stu-id="24f24-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="24f24-106">Neshoda mezi konfigurací klienta a služby SSL/TLS</span><span class="sxs-lookup"><span data-stu-id="24f24-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="24f24-107">Šablona gRPC a ukázky používají protokol [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) k zabezpečení služeb gRPC ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="24f24-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="24f24-108">gRPC klienti musí používat zabezpečené připojení k úspěšnému volání zabezpečených služeb gRPC.</span><span class="sxs-lookup"><span data-stu-id="24f24-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="24f24-109">Můžete ověřit, že služba ASP.NET Core gRPC používá protokol TLS v protokolech zapsaných při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="24f24-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="24f24-110">Služba bude naslouchat na koncovém bodu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="24f24-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="24f24-111">Klient .NET Core musí `https` v adrese serveru použít k volání zabezpečeného připojení:</span><span class="sxs-lookup"><span data-stu-id="24f24-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="24f24-112">Všechny implementace klientů gRPC podporují protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="24f24-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="24f24-113">klienti gRPC z jiných jazyků obvykle vyžadují kanál konfigurovaný pomocí `SslCredentials` .</span><span class="sxs-lookup"><span data-stu-id="24f24-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="24f24-114">`SslCredentials`Určuje certifikát, který bude klient používat, a musí se používat místo nezabezpečených přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="24f24-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="24f24-115">Příklady konfigurace různých implementací klientů gRPC k použití protokolu TLS najdete v tématu [ověřování gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="24f24-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="24f24-116">Volání služby gRPC s nedůvěryhodným/neplatným certifikátem</span><span class="sxs-lookup"><span data-stu-id="24f24-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="24f24-117">Klient .NET gRPC vyžaduje, aby služba měla důvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="24f24-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="24f24-118">Při volání služby gRPC bez důvěryhodného certifikátu se vrátí následující chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="24f24-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="24f24-119">Neošetřená výjimka.</span><span class="sxs-lookup"><span data-stu-id="24f24-119">Unhandled exception.</span></span> <span data-ttu-id="24f24-120">System .NET. http. HttpRequestException: nepovedlo se navázat připojení SSL, viz vnitřní výjimka.</span><span class="sxs-lookup"><span data-stu-id="24f24-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="24f24-121">---> System. Security. Authentication. AuthenticationException –: vzdálený certifikát je podle ověřovací procedury neplatný.</span><span class="sxs-lookup"><span data-stu-id="24f24-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="24f24-122">Tato chyba se může zobrazit, pokud testujete aplikaci místně a ASP.NET Core certifikát pro vývoj HTTPS není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="24f24-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="24f24-123">Pokyny k vyřešení tohoto problému naleznete v tématu [Trust ASP.NET Core certifikát pro vývoj https ve Windows a MacOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="24f24-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="24f24-124">Pokud voláte službu gRPC na jiném počítači a nemůžete důvěřovat certifikátu, může být klient gRPC nakonfigurovaný tak, aby ignoroval neplatný certifikát.</span><span class="sxs-lookup"><span data-stu-id="24f24-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="24f24-125">Následující kód používá [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) k povolení volání bez důvěryhodného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="24f24-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="24f24-126">Nedůvěryhodné certifikáty by se měly používat jenom při vývoji aplikací.</span><span class="sxs-lookup"><span data-stu-id="24f24-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="24f24-127">Produkční aplikace by měly vždy používat platné certifikáty.</span><span class="sxs-lookup"><span data-stu-id="24f24-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="24f24-128">Volání nezabezpečených služeb gRPC pomocí klienta .NET Core</span><span class="sxs-lookup"><span data-stu-id="24f24-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="24f24-129">Pro volání nezabezpečených služeb gRPC s klientem .NET Core je vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="24f24-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="24f24-130">Klient gRPC musí na `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` adrese serveru nastavit přepínač `true` a použít ho `http` :</span><span class="sxs-lookup"><span data-stu-id="24f24-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="24f24-131">Nepovedlo se spustit aplikaci ASP.NET Core gRPC v macOS</span><span class="sxs-lookup"><span data-stu-id="24f24-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="24f24-132">Kestrel nepodporuje HTTP/2 s TLS v macOS a ve starších verzích Windows, jako je Windows 7.</span><span class="sxs-lookup"><span data-stu-id="24f24-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="24f24-133">ASP.NET Core šablona a ukázky gRPC standardně používají protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="24f24-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="24f24-134">Při pokusu o spuštění serveru gRPC se zobrazí následující chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="24f24-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="24f24-135">Nedá se vytvořit vazba na https://localhost:5001 rozhraní IPv4 zpětné smyčky: v MacOS se nepodporuje HTTP/2 přes TLS, protože chybí podpora ALPN.</span><span class="sxs-lookup"><span data-stu-id="24f24-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="24f24-136">Pokud chcete tento problém obejít, nakonfigurujte Kestrel a klienta gRPC na použití HTTP/2 *bez* TLS.</span><span class="sxs-lookup"><span data-stu-id="24f24-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="24f24-137">To byste měli udělat jenom během vývoje.</span><span class="sxs-lookup"><span data-stu-id="24f24-137">You should only do this during development.</span></span> <span data-ttu-id="24f24-138">Nepoužíváte-li protokol TLS, budou zasílány zprávy gRPC bez šifrování.</span><span class="sxs-lookup"><span data-stu-id="24f24-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="24f24-139">Kestrel musí Konfigurovat koncový bod HTTP/2 bez TLS v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="24f24-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="24f24-140">Pokud je koncový bod HTTP/2 nakonfigurovaný bez TLS, musí být [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) koncového bodu nastaven na `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="24f24-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="24f24-141">`HttpProtocols.Http1AndHttp2`nelze použít, protože pro vyjednání HTTP/2 je vyžadován protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="24f24-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="24f24-142">Bez TLS budou všechna připojení ke koncovému bodu ve výchozím nastavení HTTP/1.1 a volání gRPC neúspěšná.</span><span class="sxs-lookup"><span data-stu-id="24f24-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="24f24-143">Klient gRPC musí být taky nakonfigurovaný tak, aby nepoužíval protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="24f24-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="24f24-144">Další informace najdete v tématu [Calling The gRPC Services with a .NET Core Client](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="24f24-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="24f24-145">HTTP/2 bez protokolu TLS by se mělo používat jenom při vývoji aplikací.</span><span class="sxs-lookup"><span data-stu-id="24f24-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="24f24-146">Provozní aplikace by měly vždycky používat zabezpečení přenosu.</span><span class="sxs-lookup"><span data-stu-id="24f24-146">Production apps should always use transport security.</span></span> <span data-ttu-id="24f24-147">Další informace najdete v tématu věnovaném [hlediskům zabezpečení v gRPC pro ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="24f24-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="24f24-148">prostředky C# gRPC nejsou generovány z kódu. Proto soubory</span><span class="sxs-lookup"><span data-stu-id="24f24-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="24f24-149">generování kódu gRPC konkrétní klienti a základní třídy služby vyžaduje soubory protobuf a nástroje, na které se odkazuje z projektu.</span><span class="sxs-lookup"><span data-stu-id="24f24-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="24f24-150">Musíte zahrnout:</span><span class="sxs-lookup"><span data-stu-id="24f24-150">You must include:</span></span>

* <span data-ttu-id="24f24-151">*. proto* soubory, které chcete použít ve `<Protobuf>` skupině položek.</span><span class="sxs-lookup"><span data-stu-id="24f24-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="24f24-152">[Importováno *. proto* ](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) musí být na soubory odkazováno v projektu.</span><span class="sxs-lookup"><span data-stu-id="24f24-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="24f24-153">Odkaz na balíček nástrojů pro gRPC nástrojů pro [gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="24f24-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="24f24-154">Další informace o generování gRPCch prostředků jazyka C# naleznete v tématu <xref:grpc/basics> .</span><span class="sxs-lookup"><span data-stu-id="24f24-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="24f24-155">ASP.NET Core webová aplikace hostující služby gRPC potřebuje jenom vygenerovanou základní třídu služby:</span><span class="sxs-lookup"><span data-stu-id="24f24-155">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="24f24-156">Klientská aplikace gRPC, která provádí volání gRPC, potřebuje jenom konkrétního vygenerovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="24f24-156">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="24f24-157">Projekty WPF nemůžou generovat gRPC prostředky C# z. Proto soubory</span><span class="sxs-lookup"><span data-stu-id="24f24-157">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="24f24-158">Projekty WPF mají [známý problém](https://github.com/dotnet/wpf/issues/810) , který brání správnému fungování generování kódu gRPC.</span><span class="sxs-lookup"><span data-stu-id="24f24-158">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="24f24-159">Všechny typy gRPC generované v projektu WPF odkazem `Grpc.Tools` a *. proto* soubory vytvoří chyby kompilace při použití:</span><span class="sxs-lookup"><span data-stu-id="24f24-159">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="24f24-160">Chyba CS0246: nepovedlo se najít typ nebo název oboru názvů ' MyGrpcServices ' (nechybí Direktiva using nebo odkaz na sestavení?)</span><span class="sxs-lookup"><span data-stu-id="24f24-160">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="24f24-161">Tento problém můžete vyřešit pomocí těchto potíží:</span><span class="sxs-lookup"><span data-stu-id="24f24-161">You can workaround this issue by:</span></span>

1. <span data-ttu-id="24f24-162">Vytvořte nový projekt knihovny tříd .NET Core.</span><span class="sxs-lookup"><span data-stu-id="24f24-162">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="24f24-163">V novém projektu přidejte odkazy pro povolení [generování kódu jazyka C# z \* \* . proto\* soubory](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="24f24-163">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="24f24-164">Přidejte odkaz na balíček do balíčku [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="24f24-164">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="24f24-165">Do skupiny položek přidejte soubory \* \* ..\* . `<Protobuf>`</span><span class="sxs-lookup"><span data-stu-id="24f24-165">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="24f24-166">V aplikaci WPF přidejte odkaz na nový projekt.</span><span class="sxs-lookup"><span data-stu-id="24f24-166">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="24f24-167">Aplikace WPF může použít gRPC generované typy z nového projektu knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="24f24-167">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
