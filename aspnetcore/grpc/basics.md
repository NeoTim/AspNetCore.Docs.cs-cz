---
title: Služby gRPC s jazykem C#
author: juntaoluo
description: Informace o základních konceptech při zápisu gRPC služby s C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
ms.openlocfilehash: 7c5ecf21124414b21f5c36b76e90bde67ac1f958
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672668"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="b3975-103">gRPC služby pomocí jazyka C\#</span><span class="sxs-lookup"><span data-stu-id="b3975-103">gRPC services with C\#</span></span>

<span data-ttu-id="b3975-104">Tento dokument popisuje koncepty jsou potřeba k zápisu [gRPC](https://grpc.io/docs/guides/) aplikací v C#.</span><span class="sxs-lookup"><span data-stu-id="b3975-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="b3975-105">Tady probíraná témata platit pro oboje [C core](https://grpc.io/blog/grpc-stacks)– aplikace založené na síť a ASP.NET Core gRPC.</span><span class="sxs-lookup"><span data-stu-id="b3975-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="b3975-106">proto soubor</span><span class="sxs-lookup"><span data-stu-id="b3975-106">proto file</span></span>

<span data-ttu-id="b3975-107">gRPC používá kontraktem přístup pro vývoj rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b3975-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="b3975-108">Protocol buffers (protobuf) se používají jako návrh jazyka IDL (Interface) ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="b3975-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="b3975-109">*.Proto* soubor obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b3975-109">The *.proto* file contains:</span></span>

* <span data-ttu-id="b3975-110">Definice služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="b3975-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="b3975-111">Zprávy odeslané mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="b3975-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="b3975-112">Další informace o syntaxi protobuf soubory, najdete v článku [oficiální dokumentaci (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="b3975-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="b3975-113">Představme si třeba, *greet.proto* souboru použitého v [začít používat gRPC service](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="b3975-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="b3975-114">Definuje `Greeter` služby.</span><span class="sxs-lookup"><span data-stu-id="b3975-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="b3975-115">`Greeter` Služba definuje `SayHello` volání.</span><span class="sxs-lookup"><span data-stu-id="b3975-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="b3975-116">`SayHello` odešle `HelloRequest` zprávy a přijímá `HelloResponse` zpráva:</span><span class="sxs-lookup"><span data-stu-id="b3975-116">`SayHello` sends a `HelloRequest` message and receives a `HelloResponse` message:</span></span>

[!code-proto[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="b3975-117">Přidání souboru .proto C\# aplikace</span><span class="sxs-lookup"><span data-stu-id="b3975-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="b3975-118">*.Proto* soubor zahrnut v projektu tak, že ji přidáte `<Protobuf>` skupiny položek:</span><span class="sxs-lookup"><span data-stu-id="b3975-118">The *.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="b3975-119">C#Podpora nástrojů pro soubory .proto</span><span class="sxs-lookup"><span data-stu-id="b3975-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="b3975-120">Balíček nástroje [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) vyžadovaných ke generování C# assety z *.proto* soubory.</span><span class="sxs-lookup"><span data-stu-id="b3975-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *.proto* files.</span></span> <span data-ttu-id="b3975-121">Vygenerovaných prostředků (soubory):</span><span class="sxs-lookup"><span data-stu-id="b3975-121">The generated assets (files):</span></span>

* <span data-ttu-id="b3975-122">Jsou generovány na základě potřeby pokaždé, když sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="b3975-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="b3975-123">Nejsou přidány do projektu nebo zapsány do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="b3975-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="b3975-124">Jsou obsaženy v artefaktu sestavení *obj* adresáře.</span><span class="sxs-lookup"><span data-stu-id="b3975-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="b3975-125">Tento balíček je vyžadován server i klient projekty.</span><span class="sxs-lookup"><span data-stu-id="b3975-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="b3975-126">`Grpc.Tools` lze přidat pomocí Správce balíčků v sadě Visual Studio nebo přidáním `<PackageReference>` do souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="b3975-126">`Grpc.Tools` can be added by using the Package Manager in Visual Studio or adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=17)]

<span data-ttu-id="b3975-127">Balíček nástroje není nutné za běhu, takže závislost je označená pomocí `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="b3975-127">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

## <a name="generated-c-assets"></a><span data-ttu-id="b3975-128">Vygeneruje C# prostředky</span><span class="sxs-lookup"><span data-stu-id="b3975-128">Generated C# assets</span></span>

<span data-ttu-id="b3975-129">Vytvoří balíček nástroje C# typy představující zpráv definovaný v zahrnutou *.proto* soubory.</span><span class="sxs-lookup"><span data-stu-id="b3975-129">The tooling package generates the C# types representing the messages defined in the included *.proto* files.</span></span>

<span data-ttu-id="b3975-130">Pro prostředky na straně serveru je vygenerována základního typu abstraktní služby.</span><span class="sxs-lookup"><span data-stu-id="b3975-130">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="b3975-131">Základní typ obsahuje definice všech součástí gRPC volání *.proto* souboru.</span><span class="sxs-lookup"><span data-stu-id="b3975-131">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="b3975-132">Vytvoření implementace konkrétní služby, který je odvozen z tohoto základního typu a implementuje logiku pro volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="b3975-132">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="b3975-133">Pro `greet.proto`, v příkladu je popsáno výše, abstraktní `GreeterBase` typ, který obsahuje virtuální `SayHello` generované metody.</span><span class="sxs-lookup"><span data-stu-id="b3975-133">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="b3975-134">Konkrétní implementaci `GreeterService` přepisuje metodu a implementuje logiku zpracování gRPC volání.</span><span class="sxs-lookup"><span data-stu-id="b3975-134">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="b3975-135">Pro prostředky na straně klienta je vygenerována typ konkrétní klienta.</span><span class="sxs-lookup"><span data-stu-id="b3975-135">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="b3975-136">GRPC zavolá v *.proto* souboru jsou přeloženy do metod na konkrétní typ, který může být volána.</span><span class="sxs-lookup"><span data-stu-id="b3975-136">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="b3975-137">Pro `greet.proto`, v příkladu je popsáno výše, konkrétní `GreeterClient` typ generován.</span><span class="sxs-lookup"><span data-stu-id="b3975-137">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="b3975-138">Volání `GreeterClient.SayHello` k zahájení gRPC volání serveru.</span><span class="sxs-lookup"><span data-stu-id="b3975-138">Call `GreeterClient.SayHello` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?highlight=5-8&name=snippet)]

<span data-ttu-id="b3975-139">Ve výchozím nastavení, prostředky serveru a klienta jsou generovány pro každou *.proto* zahrnuté v souboru `<Protobuf>` skupiny položek.</span><span class="sxs-lookup"><span data-stu-id="b3975-139">By default, server and client assets are generated for each *.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="b3975-140">Aby se serverové prostředky jsou generovány v projektu serveru, `GrpcServices` atribut je nastaven na `Server`.</span><span class="sxs-lookup"><span data-stu-id="b3975-140">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

<span data-ttu-id="b3975-141">Podobně platí, atribut je nastaven na `Client` v klientské projekty.</span><span class="sxs-lookup"><span data-stu-id="b3975-141">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3975-142">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b3975-142">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
