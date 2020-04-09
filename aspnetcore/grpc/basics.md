---
title: Služby gRPC s jazykem C#
author: juntaoluo
description: Naučte se základní pojmy při psaní služeb gRPC s C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664204"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="e84f8-103">gRPC služby s C\#</span><span class="sxs-lookup"><span data-stu-id="e84f8-103">gRPC services with C\#</span></span>

<span data-ttu-id="e84f8-104">Tento dokument popisuje koncepty potřebné k zápisu [gRPC](https://grpc.io/docs/guides/) aplikací v C#.</span><span class="sxs-lookup"><span data-stu-id="e84f8-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="e84f8-105">Témata, která jsou zde popsána, se vztahují jak na aplikace gRPC založené na [jádru C,](https://grpc.io/blog/grpc-stacks)tak ASP.NET na gRPC založené na jádru.</span><span class="sxs-lookup"><span data-stu-id="e84f8-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="e84f8-106">proto soubor</span><span class="sxs-lookup"><span data-stu-id="e84f8-106">proto file</span></span>

<span data-ttu-id="e84f8-107">gRPC používá přístup k vývoji rozhraní API založený na smlouvě.</span><span class="sxs-lookup"><span data-stu-id="e84f8-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="e84f8-108">Vyrovnávací paměti protokolu (protobuf) se ve výchozím nastavení používají jako návrhový jazyk rozhraní (IDL).</span><span class="sxs-lookup"><span data-stu-id="e84f8-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="e84f8-109">Soubor \* \*.proto\* obsahuje:</span><span class="sxs-lookup"><span data-stu-id="e84f8-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="e84f8-110">Definice služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="e84f8-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="e84f8-111">Zprávy odeslané mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="e84f8-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="e84f8-112">Další informace o syntaxi souborů protobufů naleznete v [oficiální dokumentaci (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="e84f8-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="e84f8-113">Zvažte například soubor *greet.proto* použitý v [začínáme se službou gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="e84f8-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="e84f8-114">Definuje službu. `Greeter`</span><span class="sxs-lookup"><span data-stu-id="e84f8-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="e84f8-115">Služba `Greeter` definuje `SayHello` volání.</span><span class="sxs-lookup"><span data-stu-id="e84f8-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="e84f8-116">`SayHello`odešle `HelloRequest` zprávu a `HelloReply` obdrží zprávu:</span><span class="sxs-lookup"><span data-stu-id="e84f8-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="e84f8-117">Přidání souboru .proto\# do aplikace C</span><span class="sxs-lookup"><span data-stu-id="e84f8-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="e84f8-118">Soubor \* \*.proto\* je součástí projektu přidáním `<Protobuf>` do skupiny položek:</span><span class="sxs-lookup"><span data-stu-id="e84f8-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="e84f8-119">Podpora nástrojů jazyka C# pro soubory .proto</span><span class="sxs-lookup"><span data-stu-id="e84f8-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="e84f8-120">Nástrojový balíček [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) je nutné generovat c# prostředky ze \* \*souborů .proto.\*</span><span class="sxs-lookup"><span data-stu-id="e84f8-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="e84f8-121">Generované datové zdroje (soubory):</span><span class="sxs-lookup"><span data-stu-id="e84f8-121">The generated assets (files):</span></span>

* <span data-ttu-id="e84f8-122">Jsou generovány podle potřeby při každém vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="e84f8-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="e84f8-123">Nejsou přidány do projektu nebo se změnami do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="e84f8-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="e84f8-124">Jsou artefakt sestavení obsažené v adresáři *obj.*</span><span class="sxs-lookup"><span data-stu-id="e84f8-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="e84f8-125">Tento balíček je vyžadován serverovými i klientskými projekty.</span><span class="sxs-lookup"><span data-stu-id="e84f8-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="e84f8-126">Metabalíček `Grpc.AspNetCore` obsahuje odkaz `Grpc.Tools`na .</span><span class="sxs-lookup"><span data-stu-id="e84f8-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="e84f8-127">Serverové projekty můžete přidat `Grpc.AspNetCore` pomocí Správce balíčků `<PackageReference>` v sadě Visual Studio nebo přidáním souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="e84f8-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="e84f8-128">Klientské projekty `Grpc.Tools` by měly odkazovat přímo vedle ostatních balíčků potřebných k použití klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="e84f8-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="e84f8-129">Balíček nástrojů není vyžadován za běhu, takže závislost je `PrivateAssets="All"`označena :</span><span class="sxs-lookup"><span data-stu-id="e84f8-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="e84f8-130">Generované datové zdroje jazyka C#</span><span class="sxs-lookup"><span data-stu-id="e84f8-130">Generated C# assets</span></span>

<span data-ttu-id="e84f8-131">Balíček nástrojů generuje c# typy představující zprávy definované v zahrnuté \* \*.proto\* soubory.</span><span class="sxs-lookup"><span data-stu-id="e84f8-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="e84f8-132">Pro prostředky na straně serveru je generován abstraktní typ základny služby.</span><span class="sxs-lookup"><span data-stu-id="e84f8-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="e84f8-133">Základní typ obsahuje definice všech volání gRPC obsažených v souboru *.proto.*</span><span class="sxs-lookup"><span data-stu-id="e84f8-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="e84f8-134">Vytvořte konkrétní implementaci služby, která je odvozena z tohoto základního typu a implementuje logiku pro volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="e84f8-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="e84f8-135">Pro `greet.proto`, příklad popsaný výše, `GreeterBase` abstraktní typ, `SayHello` který obsahuje virtuální metodu je generován.</span><span class="sxs-lookup"><span data-stu-id="e84f8-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="e84f8-136">Konkrétní implementace `GreeterService` přepíše metodu a implementuje logiku zpracování volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="e84f8-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="e84f8-137">Pro prostředky na straně klienta je generován konkrétní typ klienta.</span><span class="sxs-lookup"><span data-stu-id="e84f8-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="e84f8-138">Volání gRPC v souboru *.proto* jsou přeložena do metod na konkrétní typ, který lze volat.</span><span class="sxs-lookup"><span data-stu-id="e84f8-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="e84f8-139">Pro `greet.proto`, příklad popsaný výše, `GreeterClient` je generován konkrétní typ.</span><span class="sxs-lookup"><span data-stu-id="e84f8-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="e84f8-140">Volání `GreeterClient.SayHelloAsync` zahájit volání gRPC na server.</span><span class="sxs-lookup"><span data-stu-id="e84f8-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="e84f8-141">Ve výchozím nastavení jsou pro každý `<Protobuf>` \* \*\* soubor .proto zahrnutý do skupiny položek generovány datové zdroje serveru a klienta.</span><span class="sxs-lookup"><span data-stu-id="e84f8-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="e84f8-142">Chcete-li zajistit, aby byly v projektu `GrpcServices` serveru generovány `Server`pouze datové zdroje serveru, je atribut nastaven na .</span><span class="sxs-lookup"><span data-stu-id="e84f8-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="e84f8-143">Podobně atribut je nastaven `Client` na v klientských projektech.</span><span class="sxs-lookup"><span data-stu-id="e84f8-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="e84f8-144">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e84f8-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
