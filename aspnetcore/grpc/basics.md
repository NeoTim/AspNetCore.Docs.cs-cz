---
title: Služby gRPC s jazykem C#
author: juntaoluo
description: Seznamte se se základními koncepty při psaní služeb gRPC pomocí jazyka C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: a55ed90e7c854d1475b1f5d95347505fad0813ab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774753"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="5866b-103">gRPC Services s využitím C\#</span><span class="sxs-lookup"><span data-stu-id="5866b-103">gRPC services with C\#</span></span>

<span data-ttu-id="5866b-104">Tento dokument popisuje koncepty potřebné k psaní aplikací [gRPC](https://grpc.io/docs/guides/) v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="5866b-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="5866b-105">Zde uvedená témata platí pro gRPC aplikace založené na jazyce [C-Core](https://grpc.io/blog/grpc-stacks)i ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5866b-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="5866b-106">Proto soubor</span><span class="sxs-lookup"><span data-stu-id="5866b-106">proto file</span></span>

<span data-ttu-id="5866b-107">gRPC využívá přístup ke vývoji rozhraní API, který je prvním kontraktem.</span><span class="sxs-lookup"><span data-stu-id="5866b-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="5866b-108">Vyrovnávací paměti protokolu (protobuf) se ve výchozím nastavení používají jako jazyk IDL (Interface design Language).</span><span class="sxs-lookup"><span data-stu-id="5866b-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="5866b-109">Soubor \* \*... proto\* obsahuje:</span><span class="sxs-lookup"><span data-stu-id="5866b-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="5866b-110">Definice služby gRPC</span><span class="sxs-lookup"><span data-stu-id="5866b-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="5866b-111">Zprávy odesílané mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="5866b-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="5866b-112">Další informace o syntaxi souborů protobuf naleznete v [oficiální dokumentaci (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="5866b-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="5866b-113">Předpokládejme například, že soubor *....* je použit v části Začínáme [se službou gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="5866b-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="5866b-114">Definuje `Greeter` službu.</span><span class="sxs-lookup"><span data-stu-id="5866b-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="5866b-115">`Greeter` Služba definuje `SayHello` volání.</span><span class="sxs-lookup"><span data-stu-id="5866b-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="5866b-116">`SayHello`odešle `HelloRequest` zprávu a obdrží `HelloReply` zprávu:</span><span class="sxs-lookup"><span data-stu-id="5866b-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="5866b-117">Přidání souboru... a do aplikace\# v jazyce C</span><span class="sxs-lookup"><span data-stu-id="5866b-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="5866b-118">Soubor \* \*....\* je obsažen v projektu přidáním do skupiny `<Protobuf>` položek:</span><span class="sxs-lookup"><span data-stu-id="5866b-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="5866b-119">Podpora nástrojů C# pro soubory.</span><span class="sxs-lookup"><span data-stu-id="5866b-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="5866b-120">K vygenerování prostředků jazyka C# ze souborů \* \*.\* infroms se vyžaduje balíček nástrojů [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="5866b-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="5866b-121">Vygenerované prostředky (soubory):</span><span class="sxs-lookup"><span data-stu-id="5866b-121">The generated assets (files):</span></span>

* <span data-ttu-id="5866b-122">Jsou generovány podle potřeby pokaždé, když je projekt sestaven.</span><span class="sxs-lookup"><span data-stu-id="5866b-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="5866b-123">Nejsou přidány do projektu nebo vráceny se změnami do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="5866b-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="5866b-124">Jsou artefaktem sestavení obsažený v adresáři *obj* .</span><span class="sxs-lookup"><span data-stu-id="5866b-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="5866b-125">Tento balíček vyžadují projekty serveru i klienta.</span><span class="sxs-lookup"><span data-stu-id="5866b-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="5866b-126">`Grpc.AspNetCore` Metapackage obsahuje odkaz na `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="5866b-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="5866b-127">Serverové projekty lze přidat `Grpc.AspNetCore` pomocí Správce balíčků v aplikaci Visual Studio nebo přidáním `<PackageReference>` do souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="5866b-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="5866b-128">Klientské projekty by se měly `Grpc.Tools` přímo odkazovat vedle ostatních balíčků vyžadovaných k použití klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="5866b-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="5866b-129">Balíček nástrojů se za běhu nevyžaduje, takže je tato závislost označená jako `PrivateAssets="All"`:</span><span class="sxs-lookup"><span data-stu-id="5866b-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="5866b-130">Generované prostředky C#</span><span class="sxs-lookup"><span data-stu-id="5866b-130">Generated C# assets</span></span>

<span data-ttu-id="5866b-131">Balíček nástrojů generuje typy jazyka C# reprezentující zprávy definované v zahrnutých \* \*souborech.\*</span><span class="sxs-lookup"><span data-stu-id="5866b-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="5866b-132">U prostředků na straně serveru se vygeneruje abstraktní základní typ služby.</span><span class="sxs-lookup"><span data-stu-id="5866b-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="5866b-133">Základní typ obsahuje definice všech volání gRPC obsažených v souboru *..* .</span><span class="sxs-lookup"><span data-stu-id="5866b-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="5866b-134">Vytvořte konkrétní implementaci služby, která je odvozena z tohoto základního typu a implementuje logiku pro volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="5866b-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="5866b-135">Pro `greet.proto`, výše popsaný příklad, je vygenerován `GreeterBase` abstraktní typ, který obsahuje `SayHello` virtuální metodu.</span><span class="sxs-lookup"><span data-stu-id="5866b-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="5866b-136">Konkrétní implementace `GreeterService` přepisuje metodu a implementuje logiku, která zpracovává volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="5866b-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="5866b-137">U prostředků na straně klienta se vygeneruje konkrétní typ klienta.</span><span class="sxs-lookup"><span data-stu-id="5866b-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="5866b-138">Volání gRPC v souboru *.* proč jsou přeložena do metod pro konkrétní typ, který lze volat.</span><span class="sxs-lookup"><span data-stu-id="5866b-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="5866b-139">Pro `greet.proto`, výše popsaný příklad, je vytvořen konkrétní `GreeterClient` typ.</span><span class="sxs-lookup"><span data-stu-id="5866b-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="5866b-140">Zavolejte `GreeterClient.SayHelloAsync` k inicializaci volání gRPC na server.</span><span class="sxs-lookup"><span data-stu-id="5866b-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="5866b-141">Ve výchozím nastavení jsou prostředky serveru a klienta generovány pro každý `<Protobuf>` \* \*soubor. proto\* zahrnutý ve skupině položek.</span><span class="sxs-lookup"><span data-stu-id="5866b-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="5866b-142">Chcete-li zajistit, aby byly v projektu serveru generovány pouze prostředky `GrpcServices` serveru, je atribut `Server`nastaven na hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5866b-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="5866b-143">Podobně je atribut nastaven na `Client` hodnotu v klientských projektech.</span><span class="sxs-lookup"><span data-stu-id="5866b-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="5866b-144">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="5866b-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
