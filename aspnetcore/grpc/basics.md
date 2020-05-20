---
<span data-ttu-id="7324d-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7324d-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7324d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7324d-102">'Blazor'</span></span>
- <span data-ttu-id="7324d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7324d-103">'Identity'</span></span>
- <span data-ttu-id="7324d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7324d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="7324d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7324d-105">'Razor'</span></span>
- <span data-ttu-id="7324d-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7324d-106">'SignalR' uid:</span></span> 

---
# <a name="grpc-services-with-c"></a><span data-ttu-id="7324d-107">gRPC Services s využitím C\#</span><span class="sxs-lookup"><span data-stu-id="7324d-107">gRPC services with C\#</span></span>

<span data-ttu-id="7324d-108">Tento dokument popisuje koncepty potřebné k psaní aplikací [gRPC](https://grpc.io/docs/guides/) v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="7324d-108">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="7324d-109">Zde uvedená témata platí pro gRPC aplikace založené na jazyce [C-Core](https://grpc.io/blog/grpc-stacks)i ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7324d-109">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a><span data-ttu-id="7324d-110">Proto soubor</span><span class="sxs-lookup"><span data-stu-id="7324d-110">proto file</span></span>

<span data-ttu-id="7324d-111">gRPC využívá přístup ke vývoji rozhraní API, který je prvním kontraktem.</span><span class="sxs-lookup"><span data-stu-id="7324d-111">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="7324d-112">Vyrovnávací paměti protokolu (protobuf) se ve výchozím nastavení používají jako jazyk IDL (Interface design Language).</span><span class="sxs-lookup"><span data-stu-id="7324d-112">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="7324d-113">Soubor \* \* ... proto\* obsahuje:</span><span class="sxs-lookup"><span data-stu-id="7324d-113">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="7324d-114">Definice služby gRPC</span><span class="sxs-lookup"><span data-stu-id="7324d-114">The definition of the gRPC service.</span></span>
* <span data-ttu-id="7324d-115">Zprávy odesílané mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="7324d-115">The messages sent between clients and servers.</span></span>

<span data-ttu-id="7324d-116">Další informace o syntaxi souborů protobuf naleznete v [oficiální dokumentaci (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="7324d-116">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="7324d-117">Předpokládejme například, že soubor *....* je použit v části Začínáme [se službou gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="7324d-117">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="7324d-118">Definuje `Greeter` službu.</span><span class="sxs-lookup"><span data-stu-id="7324d-118">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="7324d-119">`Greeter`Služba definuje `SayHello` volání.</span><span class="sxs-lookup"><span data-stu-id="7324d-119">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="7324d-120">`SayHello`odešle `HelloRequest` zprávu a obdrží `HelloReply` zprávu:</span><span class="sxs-lookup"><span data-stu-id="7324d-120">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="7324d-121">Přidání souboru... a do aplikace v jazyce C \#</span><span class="sxs-lookup"><span data-stu-id="7324d-121">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="7324d-122">Soubor \* \* ....\* je obsažen v projektu přidáním do `<Protobuf>` skupiny položek:</span><span class="sxs-lookup"><span data-stu-id="7324d-122">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="7324d-123">Podpora nástrojů C# pro soubory.</span><span class="sxs-lookup"><span data-stu-id="7324d-123">C# Tooling support for .proto files</span></span>

<span data-ttu-id="7324d-124">K vygenerování prostředků jazyka C# ze souborů \* \* .\* infroms se vyžaduje balíček nástrojů [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="7324d-124">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="7324d-125">Vygenerované prostředky (soubory):</span><span class="sxs-lookup"><span data-stu-id="7324d-125">The generated assets (files):</span></span>

* <span data-ttu-id="7324d-126">Jsou generovány podle potřeby pokaždé, když je projekt sestaven.</span><span class="sxs-lookup"><span data-stu-id="7324d-126">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="7324d-127">Nejsou přidány do projektu nebo vráceny se změnami do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="7324d-127">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="7324d-128">Jsou artefaktem sestavení obsažený v adresáři *obj* .</span><span class="sxs-lookup"><span data-stu-id="7324d-128">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="7324d-129">Tento balíček vyžadují projekty serveru i klienta.</span><span class="sxs-lookup"><span data-stu-id="7324d-129">This package is required by both the server and client projects.</span></span> <span data-ttu-id="7324d-130">`Grpc.AspNetCore`Metapackage obsahuje odkaz na `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="7324d-130">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="7324d-131">Serverové projekty lze přidat `Grpc.AspNetCore` pomocí Správce balíčků v aplikaci Visual Studio nebo přidáním `<PackageReference>` do souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="7324d-131">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="7324d-132">Klientské projekty by se měly přímo odkazovat `Grpc.Tools` vedle ostatních balíčků vyžadovaných k použití klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="7324d-132">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="7324d-133">Balíček nástrojů se za běhu nevyžaduje, takže je tato závislost označená jako `PrivateAssets="All"` :</span><span class="sxs-lookup"><span data-stu-id="7324d-133">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="7324d-134">Generované prostředky C#</span><span class="sxs-lookup"><span data-stu-id="7324d-134">Generated C# assets</span></span>

<span data-ttu-id="7324d-135">Balíček nástrojů generuje typy jazyka C# reprezentující zprávy definované v zahrnutých souborech \* \* .\*</span><span class="sxs-lookup"><span data-stu-id="7324d-135">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="7324d-136">U prostředků na straně serveru se vygeneruje abstraktní základní typ služby.</span><span class="sxs-lookup"><span data-stu-id="7324d-136">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="7324d-137">Základní typ obsahuje definice všech volání gRPC obsažených v souboru *..* .</span><span class="sxs-lookup"><span data-stu-id="7324d-137">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="7324d-138">Vytvořte konkrétní implementaci služby, která je odvozena z tohoto základního typu a implementuje logiku pro volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="7324d-138">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="7324d-139">Pro `greet.proto` , výše popsaný příklad, `GreeterBase` je vygenerován abstraktní typ, který obsahuje virtuální `SayHello` metodu.</span><span class="sxs-lookup"><span data-stu-id="7324d-139">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="7324d-140">Konkrétní implementace `GreeterService` přepisuje metodu a implementuje logiku, která zpracovává volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="7324d-140">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="7324d-141">U prostředků na straně klienta se vygeneruje konkrétní typ klienta.</span><span class="sxs-lookup"><span data-stu-id="7324d-141">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="7324d-142">Volání gRPC v souboru *.* proč jsou přeložena do metod pro konkrétní typ, který lze volat.</span><span class="sxs-lookup"><span data-stu-id="7324d-142">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="7324d-143">Pro `greet.proto` , výše popsaný příklad, `GreeterClient` je vytvořen konkrétní typ.</span><span class="sxs-lookup"><span data-stu-id="7324d-143">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="7324d-144">Zavolejte `GreeterClient.SayHelloAsync` k inicializaci volání gRPC na server.</span><span class="sxs-lookup"><span data-stu-id="7324d-144">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="7324d-145">Ve výchozím nastavení jsou prostředky serveru a klienta generovány pro každý soubor \* \* . proto\* zahrnutý ve `<Protobuf>` skupině položek.</span><span class="sxs-lookup"><span data-stu-id="7324d-145">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="7324d-146">Chcete-li zajistit, aby byly v projektu serveru generovány pouze prostředky serveru, `GrpcServices` je atribut nastaven na hodnotu `Server` .</span><span class="sxs-lookup"><span data-stu-id="7324d-146">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="7324d-147">Podobně je atribut nastaven na hodnotu `Client` v klientských projektech.</span><span class="sxs-lookup"><span data-stu-id="7324d-147">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7324d-148">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7324d-148">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
