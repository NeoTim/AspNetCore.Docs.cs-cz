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
ms.openlocfilehash: 21f47b889014ad4ff66d4cb710aed0159298f0cc
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102887"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="3748e-103">Vytváření webových rozhraní API JSON z gRPC</span><span class="sxs-lookup"><span data-stu-id="3748e-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="3748e-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3748e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3748e-105">gRPC HTTP API je experimentální projekt, nikoli potvrzený produkt.</span><span class="sxs-lookup"><span data-stu-id="3748e-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="3748e-106">Chceme:</span><span class="sxs-lookup"><span data-stu-id="3748e-106">We want to:</span></span>
>
> * <span data-ttu-id="3748e-107">Otestujte, jestli náš přístup k vytváření webových rozhraní API JSON pro služby gRPC funguje.</span><span class="sxs-lookup"><span data-stu-id="3748e-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="3748e-108">Pokud je tento přístup užitečný pro vývojáře v rozhraní .NET, získáte zpětnou vazbu.</span><span class="sxs-lookup"><span data-stu-id="3748e-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="3748e-109">Zajistěte prosím [svůj názor](https://github.com/grpc/grpc-dotnet/issues/167) , abyste měli jistotu, že budeme sestavovat něco, co vývojáři rádi nabízí.</span><span class="sxs-lookup"><span data-stu-id="3748e-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="3748e-110">gRPC je moderní způsob, jak komunikovat mezi aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="3748e-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="3748e-111">gRPC využívá protokol HTTP/2, streamování, Protobuf a zprávy, aby se vytvořily vysoce výkonné služby v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="3748e-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="3748e-112">Jedno omezení s gRPC není možné použít na každé platformě.</span><span class="sxs-lookup"><span data-stu-id="3748e-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="3748e-113">Prohlížeče plně nepodporují HTTP/2, takže REST a JSON hlavní způsob, jak získat data do prohlížečových aplikací.</span><span class="sxs-lookup"><span data-stu-id="3748e-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="3748e-114">I s výhodami, které gRPC přináší, REST a JSON, mají v moderních aplikacích důležité místo.</span><span class="sxs-lookup"><span data-stu-id="3748e-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="3748e-115">Vytváření gRPC ***a*** webových rozhraní API JSON přináší nežádoucí režii vývoji aplikací.</span><span class="sxs-lookup"><span data-stu-id="3748e-115">Building gRPC ***and*** JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="3748e-116">Tento dokument popisuje, jak vytvářet webová rozhraní API JSON pomocí služeb gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="3748e-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="3748e-117">rozhraní HTTP API gRPC</span><span class="sxs-lookup"><span data-stu-id="3748e-117">gRPC HTTP API</span></span>

<span data-ttu-id="3748e-118">gRPC HTTP API je experimentální rozšíření pro ASP.NET Core, které vytváří RESTful rozhraní API JSON pro služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="3748e-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="3748e-119">Po nakonfigurování gRPC API API umožňuje aplikacím volat gRPC služby se známými koncepty HTTP:</span><span class="sxs-lookup"><span data-stu-id="3748e-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

* <span data-ttu-id="3748e-120">Příkazy HTTP</span><span class="sxs-lookup"><span data-stu-id="3748e-120">HTTP verbs</span></span>
* <span data-ttu-id="3748e-121">Vazba parametru URL</span><span class="sxs-lookup"><span data-stu-id="3748e-121">URL parameter binding</span></span>
* <span data-ttu-id="3748e-122">Žádosti a odpovědi JSON</span><span class="sxs-lookup"><span data-stu-id="3748e-122">JSON requests/responses</span></span>

<span data-ttu-id="3748e-123">gRPC je možné používat i pro volání služeb.</span><span class="sxs-lookup"><span data-stu-id="3748e-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="3748e-124">Využití</span><span class="sxs-lookup"><span data-stu-id="3748e-124">Usage</span></span>

1. <span data-ttu-id="3748e-125">Přidejte odkaz na balíček [Microsoft. AspNetCore. Grpc. HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span><span class="sxs-lookup"><span data-stu-id="3748e-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="3748e-126">Zaregistrujte služby v *Startup.cs* s `AddGrpcHttpApi` .</span><span class="sxs-lookup"><span data-stu-id="3748e-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="3748e-127">Přidejte do projektu soubory [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) a [Google/API/anotace.](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto)</span><span class="sxs-lookup"><span data-stu-id="3748e-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="3748e-128">Přihlaste se k metodám gRPC v *.* v souborech pomocí vazeb HTTP a tras:</span><span class="sxs-lookup"><span data-stu-id="3748e-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "v1/greeter/{name}"
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

<span data-ttu-id="3748e-129">`SayHello`Metoda gRPC se teď dá vyvolat jako gRPC + Protobuf a jako HTTP API:</span><span class="sxs-lookup"><span data-stu-id="3748e-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="3748e-130">Request `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="3748e-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="3748e-131">Základě `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="3748e-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="3748e-132">Protokoly serveru ukazují, že volání HTTP je prováděno službou gRPC.</span><span class="sxs-lookup"><span data-stu-id="3748e-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="3748e-133">rozhraní gRPC HTTP API mapuje příchozí požadavek HTTP na zprávu gRPC a potom převede zprávu odpovědi na JSON.</span><span class="sxs-lookup"><span data-stu-id="3748e-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="3748e-134">Toto je základní příklad.</span><span class="sxs-lookup"><span data-stu-id="3748e-134">This is a basic example.</span></span> <span data-ttu-id="3748e-135">Další možnosti přizpůsobení najdete v tématu [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) .</span><span class="sxs-lookup"><span data-stu-id="3748e-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="3748e-136">gRPC HTTP API vs gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="3748e-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="3748e-137">GRPC HTTP API a gRPC-web umožňují volat služby gRPC z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="3748e-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="3748e-138">Způsob, jakým se to dělá, se ale liší:</span><span class="sxs-lookup"><span data-stu-id="3748e-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="3748e-139">gRPC-web umožňuje aplikacím v prohlížeči volat služby gRPC z prohlížeče pomocí gRPC-webového klienta a Protobuf.</span><span class="sxs-lookup"><span data-stu-id="3748e-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="3748e-140">gRPC-web vyžaduje, aby aplikace v prohlížeči generovala klienta gRPC a využila výhod zasílání malých, rychlých zpráv Protobuf.</span><span class="sxs-lookup"><span data-stu-id="3748e-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="3748e-141">rozhraní gRPC HTTP API umožňuje aplikacím v prohlížeči volat služby gRPC, jako by se jednalo o rozhraní RESTful API s JSON.</span><span class="sxs-lookup"><span data-stu-id="3748e-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="3748e-142">Aplikace prohlížeče nemusí vygenerovat klienta gRPC nebo něco o gRPC.</span><span class="sxs-lookup"><span data-stu-id="3748e-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="3748e-143">Pro rozhraní HTTP API gRPC se nevytvoří žádný generovaný klient.</span><span class="sxs-lookup"><span data-stu-id="3748e-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="3748e-144">Předchozí `Greeter` službu je možné volat pomocí rozhraní API pro JavaScript v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="3748e-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="3748e-145">Experimentální stav</span><span class="sxs-lookup"><span data-stu-id="3748e-145">Experimental status</span></span>

<span data-ttu-id="3748e-146">gRPC HTTP API je experiment.</span><span class="sxs-lookup"><span data-stu-id="3748e-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="3748e-147">Není dokončený a není podporovaný.</span><span class="sxs-lookup"><span data-stu-id="3748e-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="3748e-148">Tato technologie vás zajímá a umožňuje vývojářům aplikací rychle vytvářet služby gRPC a JSON ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="3748e-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="3748e-149">Neexistuje žádný závazek na dokončení gRPC API protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="3748e-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="3748e-150">Chceme posoudit zájem vývojáře v gRPC HTTP API.</span><span class="sxs-lookup"><span data-stu-id="3748e-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="3748e-151">Pokud je gRPC API HTTP zajímavé, pošlete nám prosím [svůj názor](https://github.com/grpc/grpc-dotnet/issues/167).</span><span class="sxs-lookup"><span data-stu-id="3748e-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="3748e-152">grpc – brána</span><span class="sxs-lookup"><span data-stu-id="3748e-152">grpc-gateway</span></span>

<span data-ttu-id="3748e-153">[grpc – brána](https://grpc-ecosystem.github.io/grpc-gateway/) je další technologie pro vytváření rozhraní API RESTful JSON z grpc Services.</span><span class="sxs-lookup"><span data-stu-id="3748e-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="3748e-154">Používá stejné poznámky k mapování konceptů HTTP na služby gRPC *Services.*</span><span class="sxs-lookup"><span data-stu-id="3748e-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="3748e-155">Největší rozdíl mezi grpc-Gateway a gRPC API HTTP je grpc-Gateway používá generování kódu k vytvoření reverzního proxy server.</span><span class="sxs-lookup"><span data-stu-id="3748e-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="3748e-156">Reverzní proxy překládá RESTful volání do gRPC a pak je pošle do služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="3748e-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="3748e-157">Informace o instalaci a použití grpc-Gateway najdete v [dokumentaci k grpc-Gateway](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span><span class="sxs-lookup"><span data-stu-id="3748e-157">For installation and usage of grpc-gateway, see the [grpc-gateway documentation](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3748e-158">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="3748e-158">Additional resources</span></span>

* [<span data-ttu-id="3748e-159">dokumentace k Google. API. HttpRule</span><span class="sxs-lookup"><span data-stu-id="3748e-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
