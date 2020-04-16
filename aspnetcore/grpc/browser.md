---
title: Použití gRPC v prohlížečových aplikacích
author: jamesnk
description: Přečtěte si, jak nakonfigurovat služby gRPC na ASP.NET Core tak, aby byly volatelné z aplikací prohlížeče pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
uid: grpc/browser
ms.openlocfilehash: a20e604488b1fb919f18932599ba690bfa308f0c
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440763"
---
# <a name="use-grpc-in-browser-apps"></a>Použití gRPC v prohlížečových aplikacích

Podle [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **gRPC-Web support in .NET je experimentální**
>
> gRPC-Web pro .NET je experimentální projekt, nikoli potvrzený produkt. Chceme:
>
> * Otestujte, že náš přístup k implementaci gRPC-Web funguje.
> * Získejte zpětnou vazbu o tom, zda je tento přístup užitečný pro vývojáře rozhraní .NET ve srovnání s tradičním způsobem nastavení gRPC-Web prostřednictvím proxy serveru.
>
> Prosím, zanechte zpětnou vazbu na [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) zajistit, abychom vytvořit něco, co vývojáři rádi a jsou produktivní s.

Není možné volat službu HTTP/2 gRPC z aplikace založené na prohlížeči. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) je protokol, který umožňuje prohlížeči JavaScript a Blazor aplikace volat gRPC služby. Tento článek vysvětluje, jak používat gRPC-Web v .NET Core.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web v ASP.NET Core vs. vyslanec

Existují dvě možnosti, jak přidat gRPC-Web do aplikace ASP.NET Core:

* Podpora gRPC-Web vedle gRPC HTTP/2 v ASP.NET Core. Tato možnost používá middleware `Grpc.AspNetCore.Web` poskytované balíček.
* Použijte podporu gRPC-Web [proxy vyslance](https://www.envoyproxy.io/) k překladu gRPC-Web na gRPC HTTP/2. Přeložené volání se pak přenese do aplikace ASP.NET Core.

Každý přístup má klady a zápory. Pokud už používáte funkci Envoy jako proxy server v prostředí aplikace, může mít smysl ji použít také k poskytování podpory gRPC-Web. Pokud chcete jednoduché řešení pro gRPC-Web, který `Grpc.AspNetCore.Web` vyžaduje pouze ASP.NET Core, je dobrá volba.

## <a name="configure-grpc-web-in-aspnet-core"></a>Konfigurace gRPC-Web v ASP.NET jádru

gRPC služby hostované v ASP.NET Core lze nakonfigurovat tak, aby podporovaly gRPC-Web vedle HTTP/2 gRPC. gRPC-Web nevyžaduje žádné změny služeb. Jedinou úpravou je konfigurace spuštění.

Povolení gRPC-Web se službou ASP.NET Core gRPC:

* Přidejte odkaz na balíček [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)
* Nakonfigurujte aplikaci tak, `AddGrpcWeb` aby `UseGrpcWeb` používala gRPC-Web přidáním a *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Předcházející kód:

* Přidá middleware gRPC-Web `UseGrpcWeb`, po směrování a před koncové body.
* Určuje metodu `endpoints.MapGrpcService<GreeterService>()` podporuje gRPC-Web s `EnableGrpcWeb`. 

Případně nakonfigurujte všechny služby pro `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` podporu gRPC-Web přidáním configureservices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> Existuje známý problém, který způsobuje selhání gRPC-Web při [hostování souborem Http.sys](xref:fundamentals/servers/httpsys) v rozhraní .NET Core 3.x.
>
> Řešení pro získání gRPC-Web pracuje na Http.sys je k dispozici [zde](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web a CORS

Zabezpečení prohlížeče zabraňuje webové stránce podávat žádosti do jiné domény, než je doména, která webovou stránku obsluhovala. Toto omezení se vztahuje na provádění volání gRPC-Web s aplikacemi prohlížeče. Například aplikace prohlížeče obsluhovaná `https://www.contoso.com` aplikací je blokována voláním služeb `https://services.contoso.com`gRPC-Web hostovaných na . Cross Origin Resource Sharing (CORS) lze použít k uvolnění tohoto omezení.

Chcete-li aplikaci prohlížeče povolit volání gRPC-Web napříč počátkem, nastavte [cors v ASP.NET Core](xref:security/cors). Použijte integrovanou podporu CORS a zpřístupní záhlaví specifická pro gRPC pomocí <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>aplikace .

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Předcházející kód:

* Volání `AddCors` přidat služby CORS a konfiguruje zásady CORS, která zveřejňuje hlavičky specifické pro gRPC.
* Volání `UseCors` přidat middleware CORS po směrování a před koncové body.
* Určuje metodu, která `endpoints.MapGrpcService<GreeterService>()` `RequiresCors`podporuje CORS s .

## <a name="call-grpc-web-from-the-browser"></a>Volání gRPC-Web z prohlížeče

Aplikace prohlížeče mohou používat gRPC-Web k volání služeb gRPC. Existují určité požadavky a omezení při volání služeb gRPC s gRPC-Web z prohlížeče:

* Server musí být nakonfigurován tak, aby podporoval gRPC-Web.
* Volání datových proudů klientů a obousměrné streamování nejsou podporovány. Streamování serveru je podporováno.
* Volání služeb gRPC v jiné doméně vyžaduje [konfiguraci CORS](xref:security/cors) na serveru.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Webový klient

K dispozici je JavaScript gRPC-Web klienta. Pokyny k používání gRPC-Web z JavaScriptu najdete v [tématu napsání klientského kódu JavaScriptu pomocí gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Konfigurace gRPC-Web s klientem .NET gRPC

Klienta .NET gRPC lze nakonfigurovat tak, aby uskutečňovat volání gRPC-Web. To je užitečné pro aplikace [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) které jsou hostovány v prohlížeči a mají stejná http omezení kódu JavaScript. Volání gRPC-Web s klientem .NET je [stejné jako HTTP/2 gRPC](xref:grpc/client). Jedinou úpravou je způsob vytvoření kanálu.

Použití gRPC-Web:

* Přidejte odkaz na balíček [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)
* Ujistěte se, že odkaz na balíček [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) je 2.27.0 nebo vyšší.
* Nakonfigurujte `GrpcWebHandler`kanál tak, aby používal :

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Předcházející kód:

* Konfiguruje kanál pro použití gRPC-Web.
* Vytvoří klienta a provede volání pomocí kanálu.

Při `GrpcWebHandler` vytvoření má následující možnosti konfigurace:

* **InnerHandler**: <xref:System.Net.Http.HttpMessageHandler> Podklad, který dělá gRPC HTTP `HttpClientHandler`požadavek, například .
* **Režim**: Typ výčtu, který určuje, zda `Content-Type` `application/grpc-web` je `application/grpc-web-text`požadavek gRPC HTTP nebo .
    * `GrpcWebMode.GrpcWeb`konfiguruje obsah, který má být odeslán bez kódování. Výchozí hodnota.
    * `GrpcWebMode.GrpcWebText`konfiguruje obsah tak, aby byl kódován base64. Vyžadováno pro volání datových proudů serveru v prohlížečích.
* **HttpVersion**: `Version` HTTP protokol používaný k nastavení [httprequestmessage.version](xref:System.Net.Http.HttpRequestMessage.Version) na podkladovém gRPC HTTP požadavku. gRPC-Web nevyžaduje konkrétní verzi a nepřepisuje výchozí hodnotu, pokud není zadána.

> [!IMPORTANT]
> Generovaní klienti gRPC mají synchronizační a asynchronní metody pro volání unárních metod. Například `SayHello` je synchronizace a `SayHelloAsync` je asynchronní. Volání metody synchronizace v aplikaci Blazor WebAssembly způsobí, že aplikace přestane reagovat. Asynchronní metody musí být vždy použity v Blazor WebAssembly.

## <a name="additional-resources"></a>Další zdroje

* [gRPC pro webové klienty projekt GitHub](https://github.com/grpc/grpc-web)
* <xref:security/cors>
