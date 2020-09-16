---
title: Testování služeb pomocí nástrojů gRPC
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
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594416"
---
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="fadcd-105">Testování služeb pomocí nástrojů gRPC</span><span class="sxs-lookup"><span data-stu-id="fadcd-105">Test services with gRPC tools</span></span>

<span data-ttu-id="fadcd-106">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="fadcd-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="fadcd-107">K dispozici jsou nástroje pro gRPC, které vývojářům umožňují testovat služby bez vytváření klientských aplikací.</span><span class="sxs-lookup"><span data-stu-id="fadcd-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="fadcd-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) je nástroj příkazového řádku, který poskytuje interakce se službami gRPC.</span><span class="sxs-lookup"><span data-stu-id="fadcd-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="fadcd-109">[gRPCui](https://github.com/fullstorydev/grpcui) přidá interaktivní webové uživatelské rozhraní pro gRPC.</span><span class="sxs-lookup"><span data-stu-id="fadcd-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="fadcd-110">Tento článek popisuje, jak:</span><span class="sxs-lookup"><span data-stu-id="fadcd-110">This article discusses how to:</span></span>

* <span data-ttu-id="fadcd-111">Stáhněte a nainstalujte gRPCurl a gRPCui.</span><span class="sxs-lookup"><span data-stu-id="fadcd-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="fadcd-112">Nastavení reflexe gRPC pomocí aplikace v gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fadcd-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="fadcd-113">Zjišťování a testování gRPC služeb pomocí `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="fadcd-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="fadcd-114">Interakce s gRPC službami přes prohlížeč pomocí `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="fadcd-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="fadcd-115">O gRPCurl</span><span class="sxs-lookup"><span data-stu-id="fadcd-115">About gRPCurl</span></span>

<span data-ttu-id="fadcd-116">gRPCurl je nástroj příkazového řádku vytvořený komunitou gRPC.</span><span class="sxs-lookup"><span data-stu-id="fadcd-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="fadcd-117">Mezi tyto funkce patří:</span><span class="sxs-lookup"><span data-stu-id="fadcd-117">Its features include:</span></span>

* <span data-ttu-id="fadcd-118">Volání služeb gRPC Services, včetně služeb streamování.</span><span class="sxs-lookup"><span data-stu-id="fadcd-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="fadcd-119">Zjišťování služby pomocí [reflexe gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="fadcd-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="fadcd-120">Seznamte se s popisem služeb gRPC.</span><span class="sxs-lookup"><span data-stu-id="fadcd-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="fadcd-121">Pracuje se zabezpečeným (TLS) a nezabezpečeným (prostým) serverem.</span><span class="sxs-lookup"><span data-stu-id="fadcd-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="fadcd-122">Informace o stažení a instalaci `grpcurl` najdete na [domovské stránce GitHubu gRPCurl](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="fadcd-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="fadcd-123">Nastavit reflexi gRPC</span><span class="sxs-lookup"><span data-stu-id="fadcd-123">Setup gRPC reflection</span></span>

<span data-ttu-id="fadcd-124">`grpcurl` aby je bylo možné volat, musí znát Protobuf kontrakt služeb.</span><span class="sxs-lookup"><span data-stu-id="fadcd-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="fadcd-125">Toto lze provést dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="fadcd-125">There are two ways to do this:</span></span>

* <span data-ttu-id="fadcd-126">Použijte reflexi gRPC k zjišťování kontraktů služeb.</span><span class="sxs-lookup"><span data-stu-id="fadcd-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="fadcd-127">Zadejte soubory *.* a v argumentech příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="fadcd-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="fadcd-128">Použití gRPCurl s gRPC reflexe a Service Discovery je jednodušší.</span><span class="sxs-lookup"><span data-stu-id="fadcd-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="fadcd-129">gRPC ASP.NET Core obsahuje integrovanou podporu gRPC reflexe s balíčkem [gRPC. AspNetCore. Server. reflexe](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) .</span><span class="sxs-lookup"><span data-stu-id="fadcd-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="fadcd-130">Konfigurace reflexe v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fadcd-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="fadcd-131">Přidat `Grpc.AspNetCore.Server.Reflection` odkaz na balíček</span><span class="sxs-lookup"><span data-stu-id="fadcd-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="fadcd-132">Zaregistrujte reflexi v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fadcd-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="fadcd-133">`AddGrpcReflection` k registraci služeb, které povolují reflexi.</span><span class="sxs-lookup"><span data-stu-id="fadcd-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="fadcd-134">`MapGrpcReflectionService` pro přidání koncového bodu služby reflexe.</span><span class="sxs-lookup"><span data-stu-id="fadcd-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="fadcd-135">Použití `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="fadcd-135">Use `grpcurl`</span></span>

<span data-ttu-id="fadcd-136">`-help`Argument vysvětluje `grpcurl` Možnosti příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="fadcd-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="fadcd-137">Vyhledat služby</span><span class="sxs-lookup"><span data-stu-id="fadcd-137">Discover services</span></span>

<span data-ttu-id="fadcd-138">Pomocí `describe` příkazu Zobrazte služby definované serverem:</span><span class="sxs-lookup"><span data-stu-id="fadcd-138">Use the `describe` verb to view the services defined by the server:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe
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

<span data-ttu-id="fadcd-139">Předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="fadcd-139">The preceding example:</span></span>

* <span data-ttu-id="fadcd-140">Spustí `describe` operaci na serveru `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="fadcd-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="fadcd-141">Vytiskne služby a metody vrácené gRPC reflexe.</span><span class="sxs-lookup"><span data-stu-id="fadcd-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="fadcd-142">`Greeter` je služba implementovaná aplikací.</span><span class="sxs-lookup"><span data-stu-id="fadcd-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="fadcd-143">`ServerReflection` je služba přidaná `Grpc.AspNetCore.Server.Reflection` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="fadcd-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="fadcd-144">Kombinovat `describe` s názvem služby, metody nebo zprávy pro zobrazení jejich podrobností:</span><span class="sxs-lookup"><span data-stu-id="fadcd-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="fadcd-145">Volání služeb gRPC Services</span><span class="sxs-lookup"><span data-stu-id="fadcd-145">Call gRPC services</span></span>

<span data-ttu-id="fadcd-146">Zavolejte službu gRPC zadáním názvu služby a metody společně s argumentem JSON, který představuje zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="fadcd-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="fadcd-147">KÓD JSON se převede na Protobuf a pošle se do služby.</span><span class="sxs-lookup"><span data-stu-id="fadcd-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="fadcd-148">Předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="fadcd-148">The preceding example:</span></span>

* <span data-ttu-id="fadcd-149">`-d` argument určuje zprávu požadavku s JSON.</span><span class="sxs-lookup"><span data-stu-id="fadcd-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="fadcd-150">Tento argument musí být před názvem adresy serveru a metody.</span><span class="sxs-lookup"><span data-stu-id="fadcd-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="fadcd-151">Volá `SayHello` metodu `greeter.Greeter` služby.</span><span class="sxs-lookup"><span data-stu-id="fadcd-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="fadcd-152">Vytiskne zprávu odpovědi jako JSON.</span><span class="sxs-lookup"><span data-stu-id="fadcd-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="fadcd-153">O gRPCui</span><span class="sxs-lookup"><span data-stu-id="fadcd-153">About gRPCui</span></span>

<span data-ttu-id="fadcd-154">gRPCui je interaktivní webové uživatelské rozhraní pro gRPC.</span><span class="sxs-lookup"><span data-stu-id="fadcd-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="fadcd-155">Sestavuje se na gRPCurl a nabízí grafické rozhraní pro zjišťování a testování gRPCch služeb, podobně jako prostřednictvím nástrojů HTTP, jako je post.</span><span class="sxs-lookup"><span data-stu-id="fadcd-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="fadcd-156">Informace o stažení a instalaci `grpcui` najdete na [domovské stránce GitHubu gRPCui](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="fadcd-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="fadcd-157">Používání akce `grpcui`</span><span class="sxs-lookup"><span data-stu-id="fadcd-157">Using `grpcui`</span></span>

<span data-ttu-id="fadcd-158">Spusťte `grpcui` s adresou serveru pro interakci s jako argument.</span><span class="sxs-lookup"><span data-stu-id="fadcd-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="fadcd-159">Nástroj spustí okno prohlížeče s interaktivním webovým uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="fadcd-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="fadcd-160">služby gRPC se automaticky zjišťují pomocí reflexe gRPC.</span><span class="sxs-lookup"><span data-stu-id="fadcd-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![webové uživatelské rozhraní gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="fadcd-162">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fadcd-162">Additional resources</span></span>

* [<span data-ttu-id="fadcd-163">Domovská stránka GitHubu gRPCurl</span><span class="sxs-lookup"><span data-stu-id="fadcd-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="fadcd-164">Domovská stránka GitHubu gRPCui</span><span class="sxs-lookup"><span data-stu-id="fadcd-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="fadcd-165">Grpc. AspNetCore. Server. Reflection</span><span class="sxs-lookup"><span data-stu-id="fadcd-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
