---
title: Použití gRPC v prohlížečových aplikacích
author: jamesnk
description: Naučte se konfigurovat gRPC služby na ASP.NET Core, které se mají volat z aplikací pro prohlížeč pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 3beeffc26ffd3c2dc85bfc22a46d97d5fd78d3d0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664197"
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
> Zajistěte prosím svůj názor na [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) , abyste se ujistili, že jsme vytvořili něco, co vývojáři rádi a máte produktivní.

Nemůžete volat službu gRPC HTTP/2 z aplikace založené na prohlížeči. [gRPC-web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) je protokol, který umožňuje aplikacím Javascript a Blazor v prohlížeči volat služby gRPC. Tento článek vysvětluje, jak používat gRPC-web v .NET Core.

## <a name="configure-grpc-web-in-aspnet-core"></a>Konfigurace gRPC-web v ASP.NET Core

služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC. gRPC-web nevyžaduje žádné změny služeb. Jedinou úpravou je spuštění konfigurace.

Povolení gRPC-web pomocí služby ASP.NET Core gRPC:

* Přidejte odkaz na balíček [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Nakonfigurujte aplikaci tak, aby používala gRPC-web přidáním `AddGrpcWeb` a `UseGrpcWeb` do *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Předchozí kód:

* Přidá middleware gRPC-web middleware, `UseGrpcWeb`po směrování a před koncovými body.
* Určuje metodu `endpoints.MapGrpcService<GreeterService>()` podporuje gRPC-web s `EnableGrpcWeb`. 

Případně můžete nakonfigurovat všechny služby tak, aby podporovaly gRPC-web přidáním `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` do ConfigureServices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

### <a name="grpc-web-and-cors"></a>gRPC – web a CORS

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval. Toto omezení se vztahuje k vytváření gRPC webových volání s aplikacemi prohlížeče. Například aplikace prohlížeče obsluhovaná `https://www.contoso.com` je blokována voláním gRPC webových služeb hostovaných na `https://services.contoso.com`. Pro zmírnění tohoto omezení lze použít sdílení prostředků mezi zdroji (CORS).

Pokud chcete, aby aplikace v prohlížeči mohla dělat gRPC webová volání mezi zdroji, nastavte [CORS v ASP.NET Core](xref:security/cors). Využijte integrovanou podporu CORS a vystavte hlavičky specifické pro gRPC pomocí <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Předchozí kód:

* Volá `AddCors`, aby se přidaly služby CORS a nakonfigurují zásady CORS, které zpřístupňují hlavičky specifické pro gRPC.
* Volá `UseCors` pro přidání middlewaru CORS po směrování a před koncovými body.
* Určuje, `endpoints.MapGrpcService<GreeterService>()` metoda podporuje CORS s `RequiresCors`.

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
* Zajistěte, aby byl odkaz na balíček [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.27.0 nebo vyšší.
* Nakonfigurujte kanál tak, aby používal `GrpcWebHandler`:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Předchozí kód:

* Nakonfiguruje kanál tak, aby používal gRPC-Web.
* Vytvoří klienta a provede volání pomocí kanálu.

`GrpcWebHandler` má při vytvoření následující možnosti konfigurace:

* **InnerHandler**: podkladové <xref:System.Net.Http.HttpMessageHandler>, které vytváří požadavek gRPC http, například `HttpClientHandler`.
* **Mode**: typ výčtu určující, zda `Content-Type` požadavek HTTP gRPC je `application/grpc-web` nebo `application/grpc-web-text`.
    * `GrpcWebMode.GrpcWeb` konfiguruje obsah, který se má odeslat bez kódování. Výchozí hodnota.
    * `GrpcWebMode.GrpcWebText` konfiguruje obsah jako kódovaný v kódování Base64. Vyžaduje se pro volání streamování serveru v prohlížečích.
* **HttpVersion**: `Version` protokolu HTTP se používá k nastavení [zprávy HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na podkladové žádosti HTTP gRPC. gRPC-web nevyžaduje konkrétní verzi a nepřepisuje výchozí, pokud není zadaný.

> [!IMPORTANT]
> Vygenerované klienty gRPC mají synchronizační a asynchronní metody pro volání unárních metod. `SayHello` je například synchronizace a `SayHelloAsync` je asynchronní. Volání metody synchronizace v aplikaci Blazor WebAssembly způsobí, že aplikace přestane reagovat. Asynchronní metody musí být vždy použity v Blazor WebAssembly.

## <a name="additional-resources"></a>Další zdroje

* [gRPC pro webového klienta GitHub Project](https://github.com/grpc/grpc-web)
* <xref:security/cors>
