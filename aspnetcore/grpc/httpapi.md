---
title: Vytváření webových rozhraní API JSON z gRPC
author: jamesnk
description: Naučte se vytvářet rozhraní API protokolu JSON HTTP pro služby gRPC Services.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: fa4e7489920338344b78874690e64d4080b5a719
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805580"
---
# <a name="create-json-web-apis-from-grpc"></a>Vytváření webových rozhraní API JSON z gRPC

Od [James Newton – král](https://twitter.com/jamesnk)

> [!IMPORTANT]
> gRPC HTTP API je experimentální projekt, nikoli potvrzený produkt. Chceme:
>
> * Otestujte, jestli náš přístup k vytváření webových rozhraní API JSON pro služby gRPC funguje.
> * Pokud je tento přístup užitečný pro vývojáře v rozhraní .NET, získáte zpětnou vazbu.
>
> Zajistěte prosím [svůj názor](https://github.com/grpc/grpc-dotnet/issues/167) , abyste měli jistotu, že budeme sestavovat něco, co vývojáři rádi nabízí.

gRPC je moderní způsob, jak komunikovat mezi aplikacemi. gRPC využívá protokol HTTP/2, streamování, Protobuf a zprávy, aby se vytvořily vysoce výkonné služby v reálném čase.

Jedno omezení s gRPC není možné použít na každé platformě. Prohlížeče plně nepodporují HTTP/2, takže REST a JSON hlavní způsob, jak získat data do prohlížečových aplikací. I s výhodami, které gRPC přináší, REST a JSON, mají v moderních aplikacích důležité místo. Vytváření gRPC ***a*** webových rozhraní API JSON přináší nežádoucí režii vývoji aplikací.

Tento dokument popisuje, jak vytvářet webová rozhraní API JSON pomocí služeb gRPC Services.

## <a name="grpc-http-api"></a>rozhraní HTTP API gRPC

gRPC HTTP API je experimentální rozšíření pro ASP.NET Core, které vytváří RESTful rozhraní API JSON pro služby gRPC. Po nakonfigurování gRPC API API umožňuje aplikacím volat gRPC služby se známými koncepty HTTP:

* Příkazy HTTP
* Vazba parametru URL
* Žádosti a odpovědi JSON

gRPC je možné používat i pro volání služeb.

### <a name="usage"></a>Využití

1. Přidejte odkaz na balíček [Microsoft. AspNetCore. Grpc. HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).
1. Zaregistrujte služby v *Startup.cs* s `AddGrpcHttpApi` .
1. Přidejte do projektu soubory [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) a [Google/API/anotace.](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto)
1. Přihlaste se k metodám gRPC v *.* v souborech pomocí vazeb HTTP a tras:

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

`SayHello`Metoda gRPC se teď dá vyvolat jako gRPC + Protobuf a jako HTTP API:

* Request `HTTP/1.1 GET /v1/greeter/world`
* Základě `{ "message": "Hello world" }`

Protokoly serveru ukazují, že volání HTTP je prováděno službou gRPC. rozhraní gRPC HTTP API mapuje příchozí požadavek HTTP na zprávu gRPC a potom převede zprávu odpovědi na JSON.

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

Toto je základní příklad. Další možnosti přizpůsobení najdete v tématu [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) .

### <a name="grpc-http-api-vs-grpc-web"></a>gRPC HTTP API vs gRPC-Web

GRPC HTTP API a gRPC-web umožňují volat služby gRPC z prohlížeče. Způsob, jakým se to dělá, se ale liší:

* gRPC-web umožňuje aplikacím v prohlížeči volat služby gRPC z prohlížeče pomocí gRPC-webového klienta a Protobuf. gRPC-web vyžaduje, aby aplikace v prohlížeči generovala klienta gRPC a využila výhod zasílání malých, rychlých zpráv Protobuf.
* rozhraní gRPC HTTP API umožňuje aplikacím v prohlížeči volat služby gRPC, jako by se jednalo o rozhraní RESTful API s JSON. Aplikace prohlížeče nemusí vygenerovat klienta gRPC nebo něco o gRPC.

Pro rozhraní HTTP API gRPC se nevytvoří žádný generovaný klient. Předchozí `Greeter` službu je možné volat pomocí rozhraní API pro JavaScript v prohlížeči:

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a>Experimentální stav

gRPC HTTP API je experiment. Není dokončený a není podporovaný. Tato technologie vás zajímá a umožňuje vývojářům aplikací rychle vytvářet služby gRPC a JSON ve stejnou dobu. Neexistuje žádný závazek na dokončení gRPC API protokolu HTTP.

Chceme posoudit zájem vývojáře v gRPC HTTP API. Pokud je gRPC API HTTP zajímavé, pošlete nám prosím [svůj názor](https://github.com/grpc/grpc-dotnet/issues/167).

## <a name="grpc-gateway"></a>grpc – brána

[grpc – brána](https://grpc-ecosystem.github.io/grpc-gateway/) je další technologie pro vytváření rozhraní API RESTful JSON z grpc Services. Používá stejné poznámky k mapování konceptů HTTP na služby gRPC *Services.*

Největší rozdíl mezi grpc-Gateway a gRPC API HTTP je grpc-Gateway používá generování kódu k vytvoření reverzního proxy server. Reverzní proxy překládá RESTful volání do gRPC a pak je pošle do služby gRPC.

Informace o instalaci a použití grpc-Gateway najdete v [dokumentaci k grpc-Gateway](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).

## <a name="additional-resources"></a>Další zdroje informací

* [dokumentace k Google. API. HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
