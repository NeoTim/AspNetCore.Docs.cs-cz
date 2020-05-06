---
title: Použití gRPC v prohlížečových aplikacích
author: jamesnk
description: Naučte se konfigurovat gRPC služby na ASP.NET Core, které se mají volat z aplikací pro prohlížeč pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: a74f7acb54b4601a0c30ff1a39dc30231e2b5a78
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774740"
---
# <a name="use-grpc-in-browser-apps"></a>Použití gRPC v prohlížečových aplikacích

Od [James Newton – král](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **gRPC – webová podpora v .NET je experimentální**
>
> gRPC-web pro .NET je experimentální projekt, nikoli potvrzený produkt. Chceme:
>
> * Otestujte, jak náš přístup k implementaci gRPC-web funguje.
> * Získejte zpětnou vazbu, pokud je tento přístup užitečný pro vývojáře na platformě .NET v porovnání s tradičním způsobem nastavení gRPC-web prostřednictvím proxy serveru.
>
> Zajistěte prosím [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) svůj názor na, abyste se ujistili, že jsme vytvořili něco, co vývojáři rádi a máte produktivní.

Nemůžete volat službu gRPC HTTP/2 z aplikace založené na prohlížeči. [gRPC-web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) je protokol, který umožňuje, aby JavaScript Blazor a aplikace v prohlížeči volaly služby gRPC. Tento článek vysvětluje, jak používat gRPC-web v .NET Core.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC – web ve ASP.NET Core vs. zástupné

Existují dvě možnosti, jak přidat gRPC-web do aplikace ASP.NET Core:

* Podpora gRPC-web společně s gRPC HTTP/2 v ASP.NET Core. Tato možnost používá middleware poskytované `Grpc.AspNetCore.Web` balíčkem.
* Pomocí gRPC webu [zástupné proxy](https://www.envoyproxy.io/) můžete přeložit GRPC-web na gRPC http/2. Přeložené volání je pak předáno do aplikace ASP.NET Core.

Existují odborníci a nevýhody pro každý přístup. Pokud už používáte zástupné jako proxy v prostředí vaší aplikace, může to mít smysl použít ho také k poskytování podpory gRPC-Web. Pokud chcete jednoduché řešení pro gRPC web, které vyžaduje jenom ASP.NET Core, `Grpc.AspNetCore.Web` je vhodná volba.

## <a name="configure-grpc-web-in-aspnet-core"></a>Konfigurace gRPC-web v ASP.NET Core

služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC. gRPC-web nevyžaduje žádné změny služeb. Jedinou úpravou je spuštění konfigurace.

Povolení gRPC-web pomocí služby ASP.NET Core gRPC:

* Přidejte odkaz na balíček [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Nakonfigurujte aplikaci tak, aby používala gRPC-web `AddGrpcWeb` přidáním `UseGrpcWeb` a do *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Předcházející kód:

* Přidá gRPC-web middleware, `UseGrpcWeb`po směrování a před koncovými body.
* Určuje metodu `endpoints.MapGrpcService<GreeterService>()` , která podporuje GRPC-web `EnableGrpcWeb`s. 

Případně můžete nakonfigurovat všechny služby tak, aby podporovaly gRPC- `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` web přidáním do ConfigureServices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> K dispozici je známý problém, který způsobí selhání gRPC-web, pokud je [hostovaný souborem http. sys](xref:fundamentals/servers/httpsys) v rozhraní .NET Core 3. x.
>
> [K dispozici je](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)alternativní řešení pro získání gRPC webu na http. sys.

### <a name="grpc-web-and-cors"></a>gRPC – web a CORS

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval. Toto omezení se vztahuje k vytváření gRPC webových volání s aplikacemi prohlížeče. Například aplikace prohlížeče obsluhovaná aplikací `https://www.contoso.com` je blokována při volání gRPCch webových služeb hostovaných na `https://services.contoso.com`. Pro zmírnění tohoto omezení lze použít sdílení prostředků mezi zdroji (CORS).

Pokud chcete, aby aplikace v prohlížeči mohla dělat gRPC webová volání mezi zdroji, nastavte [CORS v ASP.NET Core](xref:security/cors). Využijte integrovanou podporu CORS a vystavte hlavičky specifické pro gRPC <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Předcházející kód:

* Volání `AddCors` pro přidání služeb CORS a nakonfigurují zásady CORS, které zveřejňují gRPC konkrétní hlavičky.
* Volání `UseCors` pro přidání middlewaru CORS po směrování a před koncovými body.
* Určuje metodu `endpoints.MapGrpcService<GreeterService>()` , která podporuje CORS `RequiresCors`s.

## <a name="call-grpc-web-from-the-browser"></a>Volání gRPC-web z prohlížeče

Aplikace prohlížeče můžou pomocí gRPC-web volat služby gRPC. Existují některé požadavky a omezení při volání služeb gRPC Services pomocí gRPC-web z prohlížeče:

* Server musí být nakonfigurovaný tak, aby podporoval gRPC-Web.
* Volání streamování klientů a obousměrného streamování nejsou podporovaná. Streamování serveru je podporované.
* Volání služeb gRPC Services v jiné doméně vyžaduje, aby na serveru byla nakonfigurovaná [CORS](xref:security/cors) .

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC – webový klient

K dispozici je gRPC JavaScript-Web Client. Pokyny, jak používat gRPC-web z JavaScriptu, najdete v tématu [Zápis kódu klienta JavaScript pomocí gRPC-web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Konfigurace gRPC-web pomocí klienta .NET gRPC

Klient .NET gRPC se dá nakonfigurovat tak, aby gRPC webová volání. To je užitečné pro [ Blazor aplikace WebAssembly](xref:blazor/index#blazor-webassembly) , které jsou hostovány v prohlížeči a mají stejná omezení http kódu jazyka JavaScript. Volání gRPC-web s klientem .NET je [stejné jako http/2 gRPC](xref:grpc/client). Jedinou úpravou je způsob vytvoření kanálu.

Použití gRPC-web:

* Přidejte odkaz na balíček [Grpc .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .
* Zajistěte, aby byl odkaz na balíček [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.27.0 nebo vyšší.
* Nakonfigurujte kanál tak, aby používal `GrpcWebHandler`:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Předcházející kód:

* Nakonfiguruje kanál tak, aby používal gRPC-Web.
* Vytvoří klienta a provede volání pomocí kanálu.

Při `GrpcWebHandler` vytvoření má následující možnosti konfigurace:

* **InnerHandler**: základní <xref:System.Net.Http.HttpMessageHandler> , který vytváří požadavek gRPC http, například `HttpClientHandler`.
* **Mode**: typ výčtu, který určuje, zda je `Content-Type` `application/grpc-web` požadavek HTTP gRPC nebo `application/grpc-web-text`.
    * `GrpcWebMode.GrpcWeb`Konfiguruje obsah, který se má odeslat bez kódování. Výchozí hodnota.
    * `GrpcWebMode.GrpcWebText`Konfiguruje obsah tak, aby byl kódovaný v kódování Base64. Vyžaduje se pro volání streamování serveru v prohlížečích.
* **HttpVersion**: protokol `Version` http použitý k nastavení [zprávy HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na podkladové žádosti HTTP gRPC. gRPC-web nevyžaduje konkrétní verzi a nepřepisuje výchozí, pokud není zadaný.

> [!IMPORTANT]
> Vygenerované klienty gRPC mají synchronizační a asynchronní metody pro volání unárních metod. Například `SayHello` je synchronizován a `SayHelloAsync` je asynchronní. Volání metody synchronizace v Blazor aplikaci WebAssembly způsobí, že aplikace přestane reagovat. Asynchronní metody musí být vždy použity v Blazor sestavení WebAssembly.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [gRPC pro webového klienta GitHub Project](https://github.com/grpc/grpc-web)
* <xref:security/cors>
