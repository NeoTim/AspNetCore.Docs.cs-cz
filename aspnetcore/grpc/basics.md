---
title: Služby gRPC s jazykem C#
author: juntaoluo
description: Informace o základních konceptech při zápisu gRPC služby s C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/basics
ms.openlocfilehash: 936561a3ad04183aff4c3ba1c9b0e8ab20dcbe12
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264774"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="b65aa-103">gRPC služby pomocí jazyka C\#</span><span class="sxs-lookup"><span data-stu-id="b65aa-103">gRPC services with C\#</span></span>

<span data-ttu-id="b65aa-104">Tento dokument popisuje základní koncepty jsou potřeba k zápisu [gRPC](https://grpc.io/docs/guides/) aplikací v C#.</span><span class="sxs-lookup"><span data-stu-id="b65aa-104">This document outlines the basic concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="b65aa-105">Tady probíraná témata platit pro oboje [C – jádro](https://grpc.io/blog/grpc-stacks) podle a gRPC aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b65aa-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks) based and ASP.NET Core based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="b65aa-106">proto soubor</span><span class="sxs-lookup"><span data-stu-id="b65aa-106">proto file</span></span>

<span data-ttu-id="b65aa-107">gRPC používá kontraktem přístup pro vývoj rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b65aa-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="b65aa-108">Protocol buffers (protobuf) se používají jako návrh jazyka IDL (Interface) ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="b65aa-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="b65aa-109">*.Proto* soubor obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b65aa-109">The *.proto* file contains:</span></span>

* <span data-ttu-id="b65aa-110">Definice služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="b65aa-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="b65aa-111">Zprávy odeslané mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="b65aa-111">The  messages sent between clients and servers.</span></span>

<span data-ttu-id="b65aa-112">Další informace o syntaxi protobuf soubory, najdete v článku [oficiální dokumentaci (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="b65aa-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="b65aa-113">Představme si třeba, *greet.proto* souboru použitého v [začít používat gRPC service](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="b65aa-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="b65aa-114">Definuje `Greeter` služby.</span><span class="sxs-lookup"><span data-stu-id="b65aa-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="b65aa-115">`Greeter` Služba definuje `SayHello` volání.</span><span class="sxs-lookup"><span data-stu-id="b65aa-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="b65aa-116">`SayHello` odešle `HelloRequest` zprávy a přijímá `HelloResponse` zpráva:</span><span class="sxs-lookup"><span data-stu-id="b65aa-116">`SayHello` sends a `HelloRequest` message and receives a `HelloResponse` message:</span></span>

[!code-proto[](~/tutorials/grpc/grpc-start/samples/GrpcStart/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="b65aa-117">Přidat soubor .proto C# aplikace</span><span class="sxs-lookup"><span data-stu-id="b65aa-117">Add a .proto file to a C# app</span></span>

<span data-ttu-id="b65aa-118">*.Proto* soubor zahrnut v projektu tak, že ji přidáte `<Protobuf>` skupiny položek:</span><span class="sxs-lookup"><span data-stu-id="b65aa-118">The *.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="b65aa-119">C#Podpora nástrojů pro soubory .proto</span><span class="sxs-lookup"><span data-stu-id="b65aa-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="b65aa-120">Balíček nástroje [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) vyžadovaných ke generování C# assety z *.proto* soubory.</span><span class="sxs-lookup"><span data-stu-id="b65aa-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *.proto* files.</span></span> <span data-ttu-id="b65aa-121">Vygenerovaných prostředků (soubory):</span><span class="sxs-lookup"><span data-stu-id="b65aa-121">The generated assets (files):</span></span>

* <span data-ttu-id="b65aa-122">Jsou generovány na základě potřeby pokaždé, když sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="b65aa-122">Are generated on a as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="b65aa-123">Nejsou přidány do projektu nebo zapsány do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="b65aa-123">Are not added to the project or checked into source control.</span></span>
* <span data-ttu-id="b65aa-124">Jsou obsaženy v artefaktu sestavení *obj* adresáře.</span><span class="sxs-lookup"><span data-stu-id="b65aa-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="b65aa-125">Tento balíček je vyžadován server i klient projekty.</span><span class="sxs-lookup"><span data-stu-id="b65aa-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="b65aa-126">`Grpc.Tools` lze přidat pomocí Správce balíčků v sadě Visual Studio nebo přidáním `<PackageReference>` do souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="b65aa-126">`Grpc.Tools` can be added by using the Package Manager in Visual Studio or adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=1&range=16)]

<span data-ttu-id="b65aa-127">Balíček nástroje není vyžadováno za běhu, proto by měly být označené závislost `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="b65aa-127">The tooling package is not required at runtime, therefore, the dependency should be marked with `PrivateAssets="All"`.</span></span>

## <a name="generated-c-assets"></a><span data-ttu-id="b65aa-128">Vygeneruje C# prostředky</span><span class="sxs-lookup"><span data-stu-id="b65aa-128">Generated C# assets</span></span>

<span data-ttu-id="b65aa-129">Bude generovat balíček nástroje C# typy představující zpráv definovaný v zahrnutou *.proto* soubory.</span><span class="sxs-lookup"><span data-stu-id="b65aa-129">The tooling package will generate the C# types representing the messages defined in the included *.proto* files.</span></span>

<span data-ttu-id="b65aa-130">Pro prostředky na straně serveru je vygenerována základního typu abstraktní služby.</span><span class="sxs-lookup"><span data-stu-id="b65aa-130">For server side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="b65aa-131">Základní typ obsahuje definice všech součástí gRPC volání *.proto* souboru.</span><span class="sxs-lookup"><span data-stu-id="b65aa-131">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="b65aa-132">Pak vytvoříte konkrétní implementace služby je odvozena z tohoto základního typu a implementuje logiku pro gRPC volání.</span><span class="sxs-lookup"><span data-stu-id="b65aa-132">You then create a concrete service implementation derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="b65aa-133">Pro `greet.proto` příklad je popsáno výše, abstraktní `GreeterBase` typ, který obsahuje virtuální `SayHello` generované metody.</span><span class="sxs-lookup"><span data-stu-id="b65aa-133">For the `greet.proto` example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="b65aa-134">Konkrétní implementaci `GreeterService` přepisuje metodu a implementuje logiku zpracování gRPC volání.</span><span class="sxs-lookup"><span data-stu-id="b65aa-134">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="b65aa-135">Pro prostředky na straně klienta je vygenerována typ konkrétní klienta.</span><span class="sxs-lookup"><span data-stu-id="b65aa-135">For client side assets, a concrete client type is generated.</span></span> <span data-ttu-id="b65aa-136">GRPC zavolá v *.proto* souboru jsou přeloženy na metody na konkrétní typ, který může být volána.</span><span class="sxs-lookup"><span data-stu-id="b65aa-136">The gRPC calls in the *.proto* file are translated to methods on the concrete type which can be called.</span></span> <span data-ttu-id="b65aa-137">Pro `greet.proto` příklad je popsáno výše, konkrétní `GreeterClient` typ generován.</span><span class="sxs-lookup"><span data-stu-id="b65aa-137">For the `greet.proto` example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="b65aa-138">`GreeterClient` Obsahuje typ `SayHello` metodu, která lze volat pro zahájení gRPC volání serveru.</span><span class="sxs-lookup"><span data-stu-id="b65aa-138">The `GreeterClient` type contains a `SayHello` method that can be called to initiate a gRPC call to the server.</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Client/Program.cs?highlight=9-11&name=snippet)]

<span data-ttu-id="b65aa-139">Ve výchozím nastavení, prostředky serveru a klienta jsou generovány pro každou *.proto* zahrnuté v souboru `<Protobuf>` skupiny položek.</span><span class="sxs-lookup"><span data-stu-id="b65aa-139">By default, both server and client assets are generated for each *.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="b65aa-140">Aby se serverové prostředky jsou generovány v projektu serveru, `GrpcServices` atribut je nastaven na `Server`.</span><span class="sxs-lookup"><span data-stu-id="b65aa-140">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

<span data-ttu-id="b65aa-141">Podobně platí, atribut je nastaven na `Client` v klientské projekty.</span><span class="sxs-lookup"><span data-stu-id="b65aa-141">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b65aa-142">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b65aa-142">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
