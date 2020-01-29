---
title: gRPC v prohlížečových aplikacích
author: jamesnk
description: Naučte se konfigurovat gRPC služby na ASP.NET Core, které se mají volat z aplikací pro prohlížeč pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/24/2020
uid: grpc/browser
ms.openlocfilehash: 6359c3b76b3cb1ba2b6d9f9a989f64cbf4c4379d
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830658"
---
# <a name="grpc-in-browser-apps"></a>gRPC v prohlížečových aplikacích

Od [James Newton – král](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **gRPC – webová podpora v .NET je experimentální**
>
> gRPC-web pro .NET je experimentální projekt, nikoli potvrzený produkt. Chceme:
>
> * Otestujte, jak náš přístup k implementaci gRPC-web funguje.
> * Získejte zpětnou vazbu, pokud je tento přístup užitečný pro vývojáře na platformě .NET v porovnání s tradičním způsobem nastavení gRPC-web prostřednictvím proxy serveru.
>
> Zajistěte prosím svůj názor na [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) , abyste se ujistili, že jsme vytvořili něco, co vývojáři rádi a máte produktivní.

Nemůžete volat službu gRPC HTTP/2 z aplikace založené na prohlížeči. [gRPC-web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) je protokol, který umožňuje aplikacím Javascript a Blazor v prohlížeči volat služby gRPC. Tento článek vysvětluje, jak používat gRPC-web v .NET Core.

## <a name="configure-grpc-web-in-aspnet-core"></a>Konfigurace gRPC-web v ASP.NET Core

služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC. gRPC-web nevyžaduje žádné změny služeb. Jedinou úpravou je spuštění konfigurace.

Povolení gRPC-web pomocí služby ASP.NET Core gRPC:

* Přidejte odkaz na balíček [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Nakonfigurujte aplikaci tak, aby používala gRPC-web přidáním `AddGrpcWeb` a `UseGrpcWeb` do *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=3,10,14)]

Předchozí kód:

* Přidá middleware gRPC-web middleware, `UseGrpcWeb`po směrování a před koncovými body.
* Určuje metodu `endpoints.MapGrpcService<GreeterService>()` podporuje gRPC-web s `EnableGrpcWeb`. 

Případně můžete nakonfigurovat všechny služby tak, aby podporovaly gRPC-web přidáním `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` do ConfigureServices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=5,12,16)]

K volání gRPC-web z prohlížeče může být potřeba některá další konfigurace, jako je například konfigurace ASP.NET Core pro podporu CORS. Další informace najdete v tématu [Podpora CORS](xref:security/cors).

## <a name="call-grpc-web-from-the-browser"></a>Volání gRPC-web z prohlížeče

Aplikace prohlížeče můžou pomocí gRPC-web volat služby gRPC. Existují některé požadavky a omezení při volání služeb gRPC Services pomocí gRPC-web z prohlížeče:

* Server musí být nakonfigurovaný tak, aby podporoval gRPC-Web.
* Volání streamování klientů a obousměrného streamování nejsou podporovaná. Streamování serveru je podporované.
* Volání služeb gRPC Services v jiné doméně vyžaduje, aby na serveru byla nakonfigurovaná [CORS](xref:security/cors) .

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC – webový klient

K dispozici je gRPC JavaScript-Web Client. Pokyny, jak používat gRPC-web z JavaScriptu, najdete v tématu [Zápis kódu klienta JavaScript pomocí gRPC-web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Konfigurace gRPC-web pomocí klienta .NET gRPC

Klient .NET gRPC se dá nakonfigurovat tak, aby gRPC webová volání. To je užitečné pro [Blazor aplikace WebAssembly](xref:blazor/index#blazor-webassembly) , které jsou hostovány v prohlížeči a mají stejná omezení http kódu jazyka JavaScript. Volání gRPC-web s klientem .NET je [stejné jako http/2 gRPC](xref:grpc/client). Jedinou úpravou je způsob vytvoření kanálu.

Použití gRPC-web:

* Přidejte odkaz na balíček [Grpc .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .
* Nakonfigurujte kanál tak, aby používal `GrpcWebHandler`:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Předchozí kód:

* Nakonfiguruje kanál tak, aby používal gRPC-Web.
* Vytvoří klienta a provede volání pomocí kanálu.

`GrpcWebHandler` má při vytvoření následující možnosti konfigurace:

* **InnerHandler**: podkladové <xref:System.Net.Http.HttpMessageHandler>, které provádí volání http, například `HttpClientHandler`.
* **Režim**: `GrpcWebMode` Enum. `GrpcWebMode.GrpcWebText` konfiguruje obsah tak, aby byl kódovaný v kódování Base64, který je nutný k podpoře volání streamování serveru.
* **HttpVersion**: `Version`protokolu HTTP. gRPC-web nevyžaduje konkrétní protokol a při vytváření požadavku ho nezadá, pokud není nakonfigurovaný.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [gRPC pro webového klienta GitHub Project](https://github.com/grpc/grpc-web)
* <xref:security/cors>
