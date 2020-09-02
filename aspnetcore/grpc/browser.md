---
title: Použití gRPC v prohlížečových aplikacích
author: jamesnk
description: Naučte se konfigurovat gRPC služby na ASP.NET Core, které se mají volat z aplikací pro prohlížeč pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 5c9501b3e7cbdcbb02e3d78d67185a0a75ccba7c
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379403"
---
# <a name="use-grpc-in-browser-apps"></a>Použití gRPC v prohlížečových aplikacích

Od [James Newton – král](https://twitter.com/jamesnk)

 Naučte se konfigurovat existující službu ASP.NET Core gRPC, která se má volat z aplikací v prohlížeči, pomocí [GRPC webového](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protokolu. gRPC-web umožňuje prohlížeči JavaScript a Blazor aplikacím volat služby gRPC. Nemůžete volat službu gRPC HTTP/2 z aplikace založené na prohlížeči. služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC.


Pokyny k přidání služby gRPC do existující aplikace ASP.NET Core najdete v tématu [Přidání gRPC Services do aplikace ASP.NET Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).

Pokyny k vytvoření projektu gRPC naleznete v tématu <xref:tutorials/grpc/grpc-start> .

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC – web ve ASP.NET Core vs. zástupné

Existují dvě možnosti, jak přidat gRPC-web do aplikace ASP.NET Core:

* Podpora gRPC-web společně s gRPC HTTP/2 v ASP.NET Core. Tato možnost používá middleware poskytované `Grpc.AspNetCore.Web` balíčkem.
* Pomocí gRPC webu [zástupné proxy](https://www.envoyproxy.io/) můžete přeložit GRPC-web na gRPC http/2. Přeložené volání je pak předáno do aplikace ASP.NET Core.

Existují odborníci a nevýhody pro každý přístup. Pokud prostředí aplikace už používá zástupné jako proxy, může to mít smysl také použít zástupné k poskytování gRPC-web support. Pro základní řešení pro gRPC-web, které vyžaduje jenom ASP.NET Core, `Grpc.AspNetCore.Web` je vhodná volba.

## <a name="configure-grpc-web-in-aspnet-core"></a>Konfigurace gRPC-web v ASP.NET Core

služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC. gRPC-web nevyžaduje žádné změny služeb. Jedinou úpravou je spuštění konfigurace.

Povolení gRPC-web pomocí služby ASP.NET Core gRPC:

* Přidejte odkaz na balíček [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Nakonfigurujte aplikaci tak, aby používala gRPC-web přidáním `UseGrpcWeb` a `EnableGrpcWeb` do *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Předcházející kód:

* Přidá gRPC-web middleware, `UseGrpcWeb` po směrování a před koncovými body.
* Určuje metodu, která `endpoints.MapGrpcService<GreeterService>()` podporuje gRPC-web s `EnableGrpcWeb` . 

Případně je možné nakonfigurovat middleware gRPC-web tak, aby všechny služby podporovaly gRPC-web ve výchozím nastavení a `EnableGrpcWeb` nejsou povinné. Určete, `new GrpcWebOptions { DefaultEnabled = true }` kdy se má middleware přidat.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> K dispozici je známý problém, který způsobí selhání gRPC-web při [hostování Http.sys](xref:fundamentals/servers/httpsys) v .NET Core 3. x.
>
> [K dispozici je](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)alternativní řešení pro získání GRPC na webu na Http.sys.

### <a name="grpc-web-and-cors"></a>gRPC – web a CORS

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval. Toto omezení se vztahuje k vytváření gRPC webových volání s aplikacemi prohlížeče. Například aplikace prohlížeče obsluhovaná aplikací `https://www.contoso.com` je blokována při volání gRPCch webových služeb hostovaných na `https://services.contoso.com` . Pro zmírnění tohoto omezení lze použít sdílení prostředků mezi zdroji (CORS).

Aby mohla aplikace v prohlížeči dělat gRPC webová volání mezi zdroji, nastavte [CORS v ASP.NET Core](xref:security/cors). Využijte integrovanou podporu CORS a vystavte hlavičky specifické pro gRPC <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> .

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Předcházející kód:

* Volání `AddCors` pro přidání služeb CORS a nakonfigurují zásady CORS, které zveřejňují gRPC konkrétní hlavičky.
* Volání `UseCors` pro přidání middlewaru CORS po směrování a před koncovými body.
* Určuje metodu, která `endpoints.MapGrpcService<GreeterService>()` podporuje CORS s `RequiresCors` .

### <a name="grpc-web-and-streaming"></a>gRPC – web a streamování

Tradiční gRPC přes HTTP/2 podporuje streamování ve všech směrech. gRPC – web nabízí omezené podpory pro streamování:

* gRPC – klienti webového prohlížeče nepodporují volání metod streamování klientů a obousměrného streamování.
* ASP.NET Core služby gRPC hostované v Azure App Service a IIS nepodporují obousměrný streamování.

Při použití gRPC-web doporučujeme použít jenom unární metody a metody streamování serveru.

## <a name="call-grpc-web-from-the-browser"></a>Volání gRPC-web z prohlížeče

Aplikace prohlížeče můžou pomocí gRPC-web volat služby gRPC. Existují některé požadavky a omezení při volání služeb gRPC Services pomocí gRPC-web z prohlížeče:

* Server musí být nakonfigurovaný tak, aby podporoval gRPC-Web.
* Volání streamování klientů a obousměrného streamování nejsou podporovaná. Streamování serveru je podporované.
* Volání služeb gRPC Services v jiné doméně vyžaduje, aby na serveru byla nakonfigurovaná [CORS](xref:security/cors) .

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC – webový klient

K dispozici je gRPC JavaScript-Web Client. Pokyny, jak používat gRPC-web z JavaScriptu, najdete v tématu [Zápis kódu klienta JavaScript pomocí gRPC-web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Konfigurace gRPC-web pomocí klienta .NET gRPC

Klient .NET gRPC se dá nakonfigurovat tak, aby gRPC webová volání. To je užitečné pro [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) aplikace, které jsou hostované v prohlížeči a mají stejná omezení http kódu JavaScriptu. Volání gRPC-web s klientem .NET je [stejné jako http/2 gRPC](xref:grpc/client). Jedinou úpravou je způsob vytvoření kanálu.

Použití gRPC-web:

* Přidejte odkaz na balíček [Grpc .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .
* Zajistěte, aby byl odkaz na balíček [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.29.0 nebo vyšší.
* Nakonfigurujte kanál tak, aby používal `GrpcWebHandler` :

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Předcházející kód:

* Nakonfiguruje kanál tak, aby používal gRPC-Web.
* Vytvoří klienta a provede volání pomocí kanálu.

`GrpcWebHandler` má následující možnosti konfigurace:

* **InnerHandler**: základní <xref:System.Net.Http.HttpMessageHandler> , který vytváří požadavek gRPC http, například `HttpClientHandler` .
* **GrpcWebMode**: typ výčtu, který určuje, zda je GRPC požadavek `Content-Type` http `application/grpc-web` nebo `application/grpc-web-text` .
    * `GrpcWebMode.GrpcWeb` Konfiguruje obsah, který se má odeslat bez kódování. Výchozí hodnota.
    * `GrpcWebMode.GrpcWebText` Konfiguruje obsah tak, aby byl kódovaný v kódování Base64. Vyžaduje se pro volání streamování serveru v prohlížečích.
* **HttpVersion**: protokol HTTP `Version` použitý k nastavení [zprávy HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na podkladové žádosti HTTP gRPC. gRPC-web nevyžaduje konkrétní verzi a nepřepisuje výchozí, pokud není zadaný.

> [!IMPORTANT]
> Vygenerované klienty gRPC mají synchronizační a asynchronní metody pro volání unárních metod. Například `SayHello` je synchronizován a `SayHelloAsync` je asynchronní. Volání metody synchronizace v Blazor WebAssembly aplikaci způsobí, že aplikace přestane reagovat. Asynchronní metody musí být vždy použity v Blazor WebAssembly .

### <a name="use-grpc-client-factory-with-grpc-web"></a>Použití klienta gRPC Client Factory s gRPC-Web

.NET Client kompatibilní s gRPC se dají vytvořit pomocí integrace gRPC s [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).

Použití gRPC-web s klientskou továrnou:

* Přidejte do souboru projektu odkazy balíčků pro následující balíčky:
  * [Grpc .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* Zaregistrujte klienta gRPC se vkládáním závislostí (DI) pomocí obecné `AddGrpcClient` metody rozšíření. V Blazor WebAssembly aplikaci jsou služby zaregistrované v di v `Program.cs` .
* Nakonfigurujte `GrpcWebHandler` pomocí <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> metody rozšíření.

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

Další informace naleznete v tématu <xref:grpc/clientfactory>.

## <a name="additional-resources"></a>Další zdroje

* [gRPC pro webového klienta GitHub Project](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
