---
page_type: sample
description: V tomto kurzu se dozvíte, jak vytvořit službu gRPC a klienta gRPC na ASP.NET Core.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: a281adc3b1fe90eeb32c1185750f911af683af83
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029093"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a><span data-ttu-id="03ce6-102">Vytvoření klienta a serveru gRPC v ASP.NET Core 3,0 pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03ce6-102">Create a gRPC client and server in ASP.NET Core 3.0 using Visual Studio</span></span>

<span data-ttu-id="03ce6-103">V tomto kurzu se dozvíte, jak vytvořit klienta .NET Core [gRPC](https://grpc.io/docs/guides/) a Server služby ASP.NET Core gRPC.</span><span class="sxs-lookup"><span data-stu-id="03ce6-103">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="03ce6-104">Na konci budete mít klienta gRPC, který komunikuje se službou Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="03ce6-104">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="03ce6-105">V tomto kurzu jste.</span><span class="sxs-lookup"><span data-stu-id="03ce6-105">In this tutorial, you;</span></span>

* <span data-ttu-id="03ce6-106">Vytvořte server gRPC.</span><span class="sxs-lookup"><span data-stu-id="03ce6-106">Create a gRPC Server.</span></span>
* <span data-ttu-id="03ce6-107">Vytvořte klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="03ce6-107">Create a gRPC client.</span></span>
* <span data-ttu-id="03ce6-108">Otestujte službu gRPC Client pomocí služby gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="03ce6-108">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="create-a-grpc-service"></a><span data-ttu-id="03ce6-109">Vytvoření služby gRPC</span><span class="sxs-lookup"><span data-stu-id="03ce6-109">Create a gRPC service</span></span>

* <span data-ttu-id="03ce6-110">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="03ce6-110">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="03ce6-111">V dialogovém okně **vytvořit nový projekt** vyberte **ASP.NET Core webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="03ce6-111">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="03ce6-112">Vybrat **Další**</span><span class="sxs-lookup"><span data-stu-id="03ce6-112">Select **Next**</span></span>
* <span data-ttu-id="03ce6-113">Pojmenujte projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="03ce6-113">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="03ce6-114">Je důležité pojmenovat projekt *GrpcGreeter* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="03ce6-114">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="03ce6-115">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="03ce6-115">Select **Create**</span></span>
* <span data-ttu-id="03ce6-116">V dialogovém okně **vytvořit novou ASP.NET Core webovou aplikaci** :</span><span class="sxs-lookup"><span data-stu-id="03ce6-116">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="03ce6-117">V rozevíracích nabídkách vyberte **.NET Core** a **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="03ce6-117">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="03ce6-118">Vyberte šablonu **služby gRPC** .</span><span class="sxs-lookup"><span data-stu-id="03ce6-118">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="03ce6-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="03ce6-119">Select **Create**</span></span>

### <a name="run-the-service"></a><span data-ttu-id="03ce6-120">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="03ce6-120">Run the service</span></span>

* <span data-ttu-id="03ce6-121">Stisknutím `Ctrl+F5` klávesy spustíte službu gRPC bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="03ce6-121">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="03ce6-122">Visual Studio spouští službu z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="03ce6-122">Visual Studio runs the service in a command prompt.</span></span>

<span data-ttu-id="03ce6-123">V protokolech se zobrazuje služba, `https://localhost:5001`na které naslouchá.</span><span class="sxs-lookup"><span data-stu-id="03ce6-123">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

> [!NOTE]
> <span data-ttu-id="03ce6-124">Šablona gRPC je nakonfigurovaná tak, aby používala [protokol TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="03ce6-124">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="03ce6-125">gRPC klienti potřebují pro volání serveru použít protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="03ce6-125">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="03ce6-126">macOS nepodporuje ASP.NET Core gRPC s protokolem TLS.</span><span class="sxs-lookup"><span data-stu-id="03ce6-126">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="03ce6-127">K úspěšnému spuštění gRPC služeb na macOS se vyžaduje další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="03ce6-127">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="03ce6-128">Další informace najdete v tématu [gRPC a ASP.NET Core v MacOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span><span class="sxs-lookup"><span data-stu-id="03ce6-128">For more information, see [gRPC and ASP.NET Core on macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="03ce6-129">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="03ce6-129">Examine the project files</span></span>

<span data-ttu-id="03ce6-130">Soubory projektu *GrpcGreeter* :</span><span class="sxs-lookup"><span data-stu-id="03ce6-130">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="03ce6-131">*greet.proto*: Soubor *....* dedefinuje `Greeter` gRPC a slouží k vygenerování prostředků serveru gRPC.</span><span class="sxs-lookup"><span data-stu-id="03ce6-131">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="03ce6-132">Další informace najdete v tématu [Úvod do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="03ce6-132">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="03ce6-133">Složka *služeb* : Obsahuje implementaci `Greeter` služby.</span><span class="sxs-lookup"><span data-stu-id="03ce6-133">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="03ce6-134">*appSettings.json*: Obsahuje konfigurační data, jako je protokol, který používá Kestrel.</span><span class="sxs-lookup"><span data-stu-id="03ce6-134">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="03ce6-135">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="03ce6-135">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="03ce6-136">*Program.cs*: Obsahuje vstupní bod pro službu gRPC.</span><span class="sxs-lookup"><span data-stu-id="03ce6-136">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="03ce6-137">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="03ce6-137">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="03ce6-138">*Startup.cs*: Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="03ce6-138">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="03ce6-139">Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="03ce6-139">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="03ce6-140">Vytvoření klienta gRPC v konzolové aplikaci .NET</span><span class="sxs-lookup"><span data-stu-id="03ce6-140">Create the gRPC client in a .NET console app</span></span>

* <span data-ttu-id="03ce6-141">Otevřete druhou instanci aplikace Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="03ce6-141">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="03ce6-142">Z řádku nabídek vyberte **soubor** > **Nový** > **projekt** .</span><span class="sxs-lookup"><span data-stu-id="03ce6-142">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="03ce6-143">V dialogovém okně **vytvořit nový projekt** vyberte konzolová **aplikace (.NET Core)** .</span><span class="sxs-lookup"><span data-stu-id="03ce6-143">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="03ce6-144">Vybrat **Další**</span><span class="sxs-lookup"><span data-stu-id="03ce6-144">Select **Next**</span></span>
* <span data-ttu-id="03ce6-145">Do textového pole **název** zadejte "GrpcGreeterClient".</span><span class="sxs-lookup"><span data-stu-id="03ce6-145">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="03ce6-146">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="03ce6-146">Select **Create**.</span></span>

### <a name="add-required-packages"></a><span data-ttu-id="03ce6-147">Přidat požadované balíčky</span><span class="sxs-lookup"><span data-stu-id="03ce6-147">Add required packages</span></span>

<span data-ttu-id="03ce6-148">Projekt klienta gRPC vyžaduje následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="03ce6-148">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="03ce6-149">[Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="03ce6-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="03ce6-150">[Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje rozhraní API pro zprávy C#Protobuf pro.</span><span class="sxs-lookup"><span data-stu-id="03ce6-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="03ce6-151">[Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/), které obsahují C# podporu nástrojů pro soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="03ce6-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="03ce6-152">Balíček nástrojů se za běhu nevyžaduje, takže závislost je označená jako `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="03ce6-152">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="03ce6-153">Nainstalujte balíčky buď pomocí konzoly Správce balíčků (PMC), nebo spravujte balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="03ce6-153">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="03ce6-154">Možnost PMC pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="03ce6-154">PMC option to install packages</span></span>

* <span data-ttu-id="03ce6-155">V aplikaci Visual Studio vyberte **nástroje** >  > **Správce balíčků NuGet** **Konzola správce balíčků** .</span><span class="sxs-lookup"><span data-stu-id="03ce6-155">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="03ce6-156">V okně **konzoly Správce balíčků** přejděte do adresáře, ve kterém existuje soubor *GrpcGreeterClient. csproj* .</span><span class="sxs-lookup"><span data-stu-id="03ce6-156">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="03ce6-157">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="03ce6-157">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="03ce6-158">Správa možností balíčků NuGet pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="03ce6-158">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="03ce6-159">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** > **Spravovat balíčky NuGet** .</span><span class="sxs-lookup"><span data-stu-id="03ce6-159">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="03ce6-160">Vyberte kartu **Procházet** .</span><span class="sxs-lookup"><span data-stu-id="03ce6-160">Select the **Browse** tab.</span></span>
* <span data-ttu-id="03ce6-161">Do vyhledávacího pole zadejte **Grpc .NET. Client** .</span><span class="sxs-lookup"><span data-stu-id="03ce6-161">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="03ce6-162">Na kartě **Procházet** vyberte balíček **Grpc .NET. Client** a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="03ce6-162">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="03ce6-163">Opakujte pro `Google.Protobuf` a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="03ce6-163">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="add-greetproto"></a><span data-ttu-id="03ce6-164">Přidat pozdrav. proto</span><span class="sxs-lookup"><span data-stu-id="03ce6-164">Add greet.proto</span></span>

* <span data-ttu-id="03ce6-165">V klientském projektu gRPC vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="03ce6-165">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="03ce6-166">Zkopírujte soubor **Protos\greet.proto** ze služby gRPC Greeter do projektu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="03ce6-166">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="03ce6-167">Upravte soubor projektu *GrpcGreeterClient. csproj* :</span><span class="sxs-lookup"><span data-stu-id="03ce6-167">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  <span data-ttu-id="03ce6-168">Klikněte pravým tlačítkem na projekt a vyberte **Upravit soubor projektu**.</span><span class="sxs-lookup"><span data-stu-id="03ce6-168">Right-click the project and select **Edit Project File**.</span></span>

* <span data-ttu-id="03ce6-169">Přidejte skupinu položek s `<Protobuf>` prvkem, který odkazuje na soubor **Greeting.** dekáže:</span><span class="sxs-lookup"><span data-stu-id="03ce6-169">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="03ce6-170">Vytvoření klienta Greeter</span><span class="sxs-lookup"><span data-stu-id="03ce6-170">Create the Greeter client</span></span>

<span data-ttu-id="03ce6-171">Sestavte projekt pro vytvoření typů v `GrpcGreeter` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="03ce6-171">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="03ce6-172">`GrpcGreeter` Typy jsou generovány automaticky procesem sestavení.</span><span class="sxs-lookup"><span data-stu-id="03ce6-172">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="03ce6-173">Aktualizujte soubor *program.cs* klienta gRPC pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="03ce6-173">Update the gRPC client *Program.cs* file with the following code:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            var httpClient = new HttpClient();
            // The port number(5001) must match the port of the gRPC server.
            httpClient.BaseAddress = new Uri("https://localhost:5001");
            var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="03ce6-174">*Program.cs* obsahuje vstupní bod a logiku pro klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="03ce6-174">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="03ce6-175">Klient s pozdravem vytvořil:</span><span class="sxs-lookup"><span data-stu-id="03ce6-175">The Greeter client is created by:</span></span>

* <span data-ttu-id="03ce6-176">Vytvoří se instance obsahující informace pro vytvoření připojení ke službě gRPC. `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="03ce6-176">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="03ce6-177">`HttpClient` Použití ke konstrukci klienta Greeter:</span><span class="sxs-lookup"><span data-stu-id="03ce6-177">Using the `HttpClient` to construct the Greeter client:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
    var reply = await client.SayHelloAsync(
                      new HelloRequest { Name = "GreeterClient" });
    Console.WriteLine("Greeting: " + reply.Message);
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

<span data-ttu-id="03ce6-178">Klient Greeter volá asynchronní `SayHello` metodu.</span><span class="sxs-lookup"><span data-stu-id="03ce6-178">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="03ce6-179">Zobrazí se výsledek `SayHello` volání:</span><span class="sxs-lookup"><span data-stu-id="03ce6-179">The result of the `SayHello` call is displayed:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
    var reply = await client.SayHelloAsync(
                      new HelloRequest { Name = "GreeterClient" });
    Console.WriteLine("Greeting: " + reply.Message);
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="03ce6-180">Test klienta gRPC pomocí služby gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="03ce6-180">Test the gRPC client with the gRPC Greeter service</span></span>

* <span data-ttu-id="03ce6-181">Ve službě Greeter stiskněte klávesu `Ctrl+F5` a spusťte server bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="03ce6-181">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="03ce6-182">V projektu stisknutím klávesy `Ctrl+F5` spusťte klienta bez ladicího programu. `GrpcGreeterClient`</span><span class="sxs-lookup"><span data-stu-id="03ce6-182">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

<span data-ttu-id="03ce6-183">Klient pošle službě pozdravu zprávu, která obsahuje název "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="03ce6-183">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="03ce6-184">Služba odešle jako odpověď zprávu "Hello GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="03ce6-184">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="03ce6-185">Odpověď "Hello GreeterClient" se zobrazí na příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="03ce6-185">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="03ce6-186">Služba gRPC zaznamenává podrobnosti o úspěšném volání do protokolů zapsaných do příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="03ce6-186">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="docs-help--next-steps-for-grpc"></a><span data-ttu-id="03ce6-187">Dokumentace k dokumentaci & další kroky pro gRPC</span><span class="sxs-lookup"><span data-stu-id="03ce6-187">Docs help & next steps for gRPC</span></span>

* [<span data-ttu-id="03ce6-188">Úvod do gRPC na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03ce6-188">Introduction to gRPC on ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [<span data-ttu-id="03ce6-189">gRPC služby sC#</span><span class="sxs-lookup"><span data-stu-id="03ce6-189">gRPC services with C#</span></span>](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [<span data-ttu-id="03ce6-190">Migrace služeb gRPC z C-Core na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03ce6-190">Migrating gRPC services from C-core to ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
