---
title: Testování gRPC služeb pomocí gRPCurl v ASP.NET Core
author: jamesnk
description: Naučte se testovat služby pomocí nástrojů gRPC. gRPCurl nástroj příkazového řádku pro interakci s gRPC službami. gRPCui je interaktivní webové uživatelské rozhraní.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: 800b320413552e73f05e0359e67eeb2caf4e0e2a
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770165"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="d8e7f-105">Testování gRPC služeb pomocí gRPCurl v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8e7f-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="d8e7f-106">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d8e7f-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d8e7f-107">K dispozici jsou nástroje pro gRPC, které vývojářům umožňují testovat služby bez vytváření klientských aplikací:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="d8e7f-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) je nástroj příkazového řádku, který poskytuje interakce se službami gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="d8e7f-109">[gRPCui](https://github.com/fullstorydev/grpcui) sestaví gRPCurl a přidá interaktivní webové uživatelské rozhraní pro gRPC, podobně jako pro nástroj post a Swagger.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="d8e7f-110">Tento článek popisuje, jak:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-110">This article discusses how to:</span></span>

* <span data-ttu-id="d8e7f-111">Stáhněte a nainstalujte gRPCurl a gRPCui.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="d8e7f-112">Nastavte reflexi gRPC pomocí aplikace gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="d8e7f-113">Zjišťování a testování gRPC služeb pomocí `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="d8e7f-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="d8e7f-114">Interakce s gRPC službami přes prohlížeč pomocí `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="d8e7f-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="d8e7f-115">O gRPCurl</span><span class="sxs-lookup"><span data-stu-id="d8e7f-115">About gRPCurl</span></span>

<span data-ttu-id="d8e7f-116">gRPCurl je nástroj příkazového řádku vytvořený komunitou gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="d8e7f-117">Mezi tyto funkce patří:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-117">Its features include:</span></span>

* <span data-ttu-id="d8e7f-118">Volání služeb gRPC Services, včetně služeb streamování.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="d8e7f-119">Zjišťování služby pomocí [reflexe gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="d8e7f-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="d8e7f-120">Seznamte se s popisem služeb gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="d8e7f-121">Pracuje se zabezpečeným (TLS) a nezabezpečeným (prostým) serverem.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="d8e7f-122">Informace o stažení a instalaci `grpcurl` najdete na [domovské stránce GitHubu gRPCurl](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="d8e7f-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![příkazový řádek gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="d8e7f-124">Nastavení reflexe gRPC</span><span class="sxs-lookup"><span data-stu-id="d8e7f-124">Set up gRPC reflection</span></span>

<span data-ttu-id="d8e7f-125">`grpcurl` před voláním služby musíte znát Protobuf kontrakt služeb.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="d8e7f-126">Toto lze provést dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-126">There are two ways to do this:</span></span>

* <span data-ttu-id="d8e7f-127">Nastavte [reflexi gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) na serveru.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="d8e7f-128">gRPCurl automaticky zjišťuje kontrakty služby.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="d8e7f-129">Zadejte `.proto` soubory v argumentech příkazového řádku pro gRPCurl.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="d8e7f-130">Použití gRPCurl s reflexí gRPC je jednodušší.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="d8e7f-131">reflexe gRPC přidá do aplikace novou službu gRPC, kterou klienti můžou volat pro zjišťování služeb.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="d8e7f-132">gRPC ASP.NET Core obsahuje integrovanou podporu pro reflexi gRPC s [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) balíčkem.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="d8e7f-133">Konfigurace reflexe v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="d8e7f-134">Přidejte `Grpc.AspNetCore.Server.Reflection` odkaz na balíček.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="d8e7f-135">Registrovat reflexi v `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="d8e7f-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="d8e7f-136">`AddGrpcReflection` k registraci služeb, které povolují reflexi.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="d8e7f-137">`MapGrpcReflectionService` pro přidání koncového bodu služby Reflection.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="d8e7f-138">Když je nastavená reflexe gRPC:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="d8e7f-139">Do serverové aplikace se přidá služba reflexe gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="d8e7f-140">Klientské aplikace, které podporují reflexi gRPC, mohou volat službu reflexe za účelem zjišťování služeb hostovaných serverem.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="d8e7f-141">služby gRPC Services se pořád volají z klienta.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="d8e7f-142">Reflexe umožňuje jenom zjišťování služby a obchází zabezpečení na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="d8e7f-143">Koncové body chráněné [ověřováním a autorizací](xref:grpc/authn-and-authz) vyžadují volajícímu předání přihlašovacích údajů pro koncový bod, který se má úspěšně volat.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="d8e7f-144">Použití `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="d8e7f-144">Use `grpcurl`</span></span>

<span data-ttu-id="d8e7f-145">`-help`Argument vysvětluje `grpcurl` Možnosti příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="d8e7f-146">Vyhledat služby</span><span class="sxs-lookup"><span data-stu-id="d8e7f-146">Discover services</span></span>

<span data-ttu-id="d8e7f-147">Pomocí `describe` příkazu Zobrazte služby definované serverem:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-147">Use the `describe` verb to view the services defined by the server:</span></span>

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

<span data-ttu-id="d8e7f-148">Předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-148">The preceding example:</span></span>

* <span data-ttu-id="d8e7f-149">Spustí `describe` operaci na serveru `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="d8e7f-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="d8e7f-150">Vytiskne služby a metody vrácené gRPC reflexe.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="d8e7f-151">`Greeter` je služba implementovaná aplikací.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="d8e7f-152">`ServerReflection` je služba přidaná `Grpc.AspNetCore.Server.Reflection` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="d8e7f-153">`describe`V kombinaci s názvem služby, metody nebo zprávy zobrazíte její podrobnosti:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="d8e7f-154">Volání služeb gRPC Services</span><span class="sxs-lookup"><span data-stu-id="d8e7f-154">Call gRPC services</span></span>

<span data-ttu-id="d8e7f-155">Zavolejte službu gRPC zadáním názvu služby a metody společně s argumentem JSON, který představuje zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="d8e7f-156">KÓD JSON se převede na Protobuf a pošle se do služby.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="d8e7f-157">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-157">In the preceding example:</span></span>

* <span data-ttu-id="d8e7f-158">`-d`Argument určuje zprávu požadavku s JSON.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="d8e7f-159">Tento argument musí být před názvem adresy serveru a metody.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="d8e7f-160">Volá `SayHello` metodu `greeter.Greeter` služby.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="d8e7f-161">Vytiskne zprávu odpovědi jako JSON.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="d8e7f-162">O gRPCui</span><span class="sxs-lookup"><span data-stu-id="d8e7f-162">About gRPCui</span></span>

<span data-ttu-id="d8e7f-163">gRPCui je interaktivní webové uživatelské rozhraní pro gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="d8e7f-164">Sestavuje se na gRPCurl a nabízí grafické rozhraní pro zjišťování a testování gRPCch služeb, podobně jako prostřednictvím nástroje HTTP, jako je například post nebo Swagger UI.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="d8e7f-165">Informace o stažení a instalaci `grpcui` najdete na [domovské stránce GitHubu gRPCui](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="d8e7f-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="d8e7f-166">Používání akce `grpcui`</span><span class="sxs-lookup"><span data-stu-id="d8e7f-166">Using `grpcui`</span></span>

<span data-ttu-id="d8e7f-167">Spusťte `grpcui` s adresou serveru pro interakci s jako argument:</span><span class="sxs-lookup"><span data-stu-id="d8e7f-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="d8e7f-168">Nástroj spustí okno prohlížeče s interaktivním webovým uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="d8e7f-169">služby gRPC se automaticky zjišťují pomocí reflexe gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8e7f-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![webové uživatelské rozhraní gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="d8e7f-171">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d8e7f-171">Additional resources</span></span>

* [<span data-ttu-id="d8e7f-172">Domovská stránka GitHubu gRPCurl</span><span class="sxs-lookup"><span data-stu-id="d8e7f-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="d8e7f-173">Domovská stránka GitHubu gRPCui</span><span class="sxs-lookup"><span data-stu-id="d8e7f-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
