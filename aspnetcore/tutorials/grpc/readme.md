---
page_type: sample
description: Tento kurz ukazuje, jak vytvořit gRPC service a gRPC klienta na ASP.NET Core.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: b9feb9eed62177358fffc0d7da582f625a431e32
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660928"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a><span data-ttu-id="1205b-102">Vytvoření klienta a serveru gRPC v ASP.NET Core 3.0 pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1205b-102">Create a gRPC client and server in ASP.NET Core 3.0 using Visual Studio</span></span>

<span data-ttu-id="1205b-103">Tento kurz ukazuje, jak vytvořit klienta [gRPC](https://grpc.io/docs/guides/) .NET Core a ASP.NET Core gRPC Server.</span><span class="sxs-lookup"><span data-stu-id="1205b-103">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="1205b-104">Na konci budete mít gRPC klienta, který komunikuje se službou gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="1205b-104">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="1205b-105">V tomto tutoriálu, vy;</span><span class="sxs-lookup"><span data-stu-id="1205b-105">In this tutorial, you;</span></span>

* <span data-ttu-id="1205b-106">Vytvořte gRPC Server.</span><span class="sxs-lookup"><span data-stu-id="1205b-106">Create a gRPC Server.</span></span>
* <span data-ttu-id="1205b-107">Vytvořte gRPC klienta.</span><span class="sxs-lookup"><span data-stu-id="1205b-107">Create a gRPC client.</span></span>
* <span data-ttu-id="1205b-108">Otestujte klientskou službu gRPC se službou gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="1205b-108">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="create-a-grpc-service"></a><span data-ttu-id="1205b-109">Vytvoření služby gRPC</span><span class="sxs-lookup"><span data-stu-id="1205b-109">Create a gRPC service</span></span>

* <span data-ttu-id="1205b-110">V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="1205b-110">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1205b-111">V **dialogovém** okně Vytvořit nový projekt vyberte **ASP.NET základní webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="1205b-111">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="1205b-112">Vybrat **další**</span><span class="sxs-lookup"><span data-stu-id="1205b-112">Select **Next**</span></span>
* <span data-ttu-id="1205b-113">Název projektu **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="1205b-113">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="1205b-114">Je důležité pojmenovat projekt *GrpcGreeter,* aby se jmenné prostory shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="1205b-114">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="1205b-115">Vybrat **vytvořit**</span><span class="sxs-lookup"><span data-stu-id="1205b-115">Select **Create**</span></span>
* <span data-ttu-id="1205b-116">V **dialogovém okně Vytvořit novou ASP.NET základní webovou aplikaci:**</span><span class="sxs-lookup"><span data-stu-id="1205b-116">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="1205b-117">V rozevíracích nabídkách vyberte **.NET Core** a **ASP.NET Core 3.0.**</span><span class="sxs-lookup"><span data-stu-id="1205b-117">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="1205b-118">Vyberte šablonu **služby gRPC.**</span><span class="sxs-lookup"><span data-stu-id="1205b-118">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="1205b-119">Vybrat **vytvořit**</span><span class="sxs-lookup"><span data-stu-id="1205b-119">Select **Create**</span></span>

### <a name="run-the-service"></a><span data-ttu-id="1205b-120">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="1205b-120">Run the service</span></span>

* <span data-ttu-id="1205b-121">Stisknutím `Ctrl+F5` klávesy spusťte službu gRPC bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="1205b-121">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="1205b-122">Visual Studio spustí službu v příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="1205b-122">Visual Studio runs the service in a command prompt.</span></span>

<span data-ttu-id="1205b-123">Protokoly zobrazit službu `https://localhost:5001`naslouchání na .</span><span class="sxs-lookup"><span data-stu-id="1205b-123">The logs show the service listening on `https://localhost:5001`.</span></span>

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
> <span data-ttu-id="1205b-124">Šablona gRPC je nakonfigurována pro použití [zabezpečení transportní vrstvy (TLS).](https://tools.ietf.org/html/rfc5246)</span><span class="sxs-lookup"><span data-stu-id="1205b-124">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="1205b-125">gRPC klienti musí používat PROTOKOL HTTPS pro volání serveru.</span><span class="sxs-lookup"><span data-stu-id="1205b-125">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="1205b-126">macOS nepodporuje ASP.NET Core gRPC s TLS.</span><span class="sxs-lookup"><span data-stu-id="1205b-126">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="1205b-127">K úspěšnému spuštění služeb gRPC v systému macOS je nutná další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1205b-127">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="1205b-128">Další informace najdete [v tématu gRPC a ASP.NET Core v macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span><span class="sxs-lookup"><span data-stu-id="1205b-128">For more information, see [gRPC and ASP.NET Core on macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="1205b-129">Prohlédněte si soubory projektu</span><span class="sxs-lookup"><span data-stu-id="1205b-129">Examine the project files</span></span>

<span data-ttu-id="1205b-130">*GrpcGreeter* soubory projektu:</span><span class="sxs-lookup"><span data-stu-id="1205b-130">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="1205b-131">*greet.proto*: Soubor *Protos/greet.proto* `Greeter` definuje gRPC a používá se ke generování prostředků serveru gRPC.</span><span class="sxs-lookup"><span data-stu-id="1205b-131">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="1205b-132">Další informace naleznete [v tématu Úvod do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="1205b-132">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="1205b-133">*Složka služby:* Obsahuje `Greeter` implementaci služby.</span><span class="sxs-lookup"><span data-stu-id="1205b-133">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="1205b-134">*appSettings.json*: Obsahuje konfigurační data, například protokol používaný kestrelem.</span><span class="sxs-lookup"><span data-stu-id="1205b-134">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="1205b-135">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1205b-135">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="1205b-136">*Program.cs*: Obsahuje vstupní bod pro službu gRPC.</span><span class="sxs-lookup"><span data-stu-id="1205b-136">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="1205b-137">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="1205b-137">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="1205b-138">*Startup.cs*: Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="1205b-138">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="1205b-139">Další informace naleznete v [tématu App startup](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="1205b-139">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="1205b-140">Vytvoření klienta gRPC v aplikaci konzoly .NET</span><span class="sxs-lookup"><span data-stu-id="1205b-140">Create the gRPC client in a .NET console app</span></span>

* <span data-ttu-id="1205b-141">Otevřete druhou instanci sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1205b-141">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="1205b-142">Na řádku nabídek vyberte **Soubor** > **nového** > **projektu.**</span><span class="sxs-lookup"><span data-stu-id="1205b-142">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="1205b-143">V **dialogovém** okně Vytvořit nový projekt vyberte **Console App (.NET Core)**.</span><span class="sxs-lookup"><span data-stu-id="1205b-143">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="1205b-144">Vybrat **další**</span><span class="sxs-lookup"><span data-stu-id="1205b-144">Select **Next**</span></span>
* <span data-ttu-id="1205b-145">Do textového pole **Název** zadejte "GrpcGreeterClient".</span><span class="sxs-lookup"><span data-stu-id="1205b-145">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="1205b-146">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1205b-146">Select **Create**.</span></span>

### <a name="add-required-packages"></a><span data-ttu-id="1205b-147">Přidání požadovaných balíčků</span><span class="sxs-lookup"><span data-stu-id="1205b-147">Add required packages</span></span>

<span data-ttu-id="1205b-148">Projekt klienta gRPC vyžaduje následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="1205b-148">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="1205b-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1205b-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="1205b-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje protobufové zprávy API pro C#.</span><span class="sxs-lookup"><span data-stu-id="1205b-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="1205b-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), který obsahuje podporu nástrojů C# pro soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="1205b-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="1205b-152">Balíček nástrojů není vyžadován za běhu, takže závislost je `PrivateAssets="All"`označena .</span><span class="sxs-lookup"><span data-stu-id="1205b-152">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="1205b-153">Nainstalujte balíčky pomocí konzoly Správce balíčků (PMC) nebo Spravovat balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="1205b-153">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="1205b-154">Možnost PMC pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="1205b-154">PMC option to install packages</span></span>

* <span data-ttu-id="1205b-155">V sadě Visual Studio vyberte **Nástroje, které** > **nástrojiNuGet Správce** > **balíčků konzoly Správce balíčků**</span><span class="sxs-lookup"><span data-stu-id="1205b-155">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="1205b-156">Z okna **Konzoly správce balíčků** přejděte do adresáře, ve kterém existuje soubor *GrpcGreeterClient.csproj.*</span><span class="sxs-lookup"><span data-stu-id="1205b-156">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="1205b-157">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="1205b-157">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="1205b-158">Možnost Spravovat balíčky NuGet pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="1205b-158">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="1205b-159">Klikněte pravým tlačítkem myši na projekt v **Průzkumníku** > řešení**Spravovat balíčky NuGet**</span><span class="sxs-lookup"><span data-stu-id="1205b-159">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="1205b-160">Vyberte kartu **Procházet**.</span><span class="sxs-lookup"><span data-stu-id="1205b-160">Select the **Browse** tab.</span></span>
* <span data-ttu-id="1205b-161">Do vyhledávacího pole zadejte **Grpc.Net.Client.**</span><span class="sxs-lookup"><span data-stu-id="1205b-161">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="1205b-162">Na kartě **Procházet** vyberte balíček **Grpc.Net.Client** a vyberte **Instalovat**.</span><span class="sxs-lookup"><span data-stu-id="1205b-162">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="1205b-163">Opakujte `Google.Protobuf` `Grpc.Tools`pro a .</span><span class="sxs-lookup"><span data-stu-id="1205b-163">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="add-greetproto"></a><span data-ttu-id="1205b-164">Přidat greet.proto</span><span class="sxs-lookup"><span data-stu-id="1205b-164">Add greet.proto</span></span>

* <span data-ttu-id="1205b-165">Vytvořte složku **Protos** v projektu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="1205b-165">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="1205b-166">Zkopírujte soubor **Protos\greet.proto** ze služby gRPC Greeter do klientského projektu gRPC.</span><span class="sxs-lookup"><span data-stu-id="1205b-166">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="1205b-167">Upravte soubor projektu *GrpcGreeterClient.csproj:*</span><span class="sxs-lookup"><span data-stu-id="1205b-167">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  <span data-ttu-id="1205b-168">Klepněte pravým tlačítkem myši na projekt a vyberte **upravit soubor projektu**.</span><span class="sxs-lookup"><span data-stu-id="1205b-168">Right-click the project and select **Edit Project File**.</span></span>

* <span data-ttu-id="1205b-169">Přidejte skupinu `<Protobuf>` položek s prvkem, který odkazuje na soubor **greet.proto:**</span><span class="sxs-lookup"><span data-stu-id="1205b-169">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="1205b-170">Vytvoření klienta Greeter</span><span class="sxs-lookup"><span data-stu-id="1205b-170">Create the Greeter client</span></span>

<span data-ttu-id="1205b-171">Vytvořte projekt a vytvořte `GrpcGreeter` typy v oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="1205b-171">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="1205b-172">Typy `GrpcGreeter` jsou generovány automaticky procesem sestavení.</span><span class="sxs-lookup"><span data-stu-id="1205b-172">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="1205b-173">Aktualizujte soubor *klienta gRPC Program.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1205b-173">Update the gRPC client *Program.cs* file with the following code:</span></span>

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
            // The port number(5001) must match the port of the gRPC server.
            var channel = GrpcChannel.ForAddress("https://localhost:5001");
            var client = new Greeter.GreeterClient(channel);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="1205b-174">*Program.cs* obsahuje vstupní bod a logiku pro klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="1205b-174">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="1205b-175">Klient Greeter je vytvořen:</span><span class="sxs-lookup"><span data-stu-id="1205b-175">The Greeter client is created by:</span></span>

* <span data-ttu-id="1205b-176">Vytvoření instance `GrpcChannel` obsahující informace pro vytvoření připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="1205b-176">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="1205b-177">Pomocí `GrpcChannel` k vytvoření klienta Greeter.</span><span class="sxs-lookup"><span data-stu-id="1205b-177">Using the `GrpcChannel` to construct the Greeter client.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="1205b-178">Otestujte klienta gRPC pomocí služby gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="1205b-178">Test the gRPC client with the gRPC Greeter service</span></span>

* <span data-ttu-id="1205b-179">Ve službě Greeter `Ctrl+F5` spusťte server bez ladicího programu stisknutím klávesy .</span><span class="sxs-lookup"><span data-stu-id="1205b-179">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="1205b-180">V `GrpcGreeterClient` projektu stisknutím `Ctrl+F5` klávesy spusťte klienta bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="1205b-180">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

<span data-ttu-id="1205b-181">Klient odešle službu pozdrav se zprávou obsahující jeho název "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="1205b-181">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="1205b-182">Služba odešle zprávu "Hello GreeterClient" jako odpověď.</span><span class="sxs-lookup"><span data-stu-id="1205b-182">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="1205b-183">V příkazovém řádku se zobrazí odpověď "Hello GreeterClient":</span><span class="sxs-lookup"><span data-stu-id="1205b-183">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="1205b-184">Služba gRPC zaznamenává podrobnosti úspěšného volání v protokolech zapsaných do příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="1205b-184">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="docs-help--next-steps-for-grpc"></a><span data-ttu-id="1205b-185">Dokumenty pomáhají & další kroky pro gRPC</span><span class="sxs-lookup"><span data-stu-id="1205b-185">Docs help & next steps for gRPC</span></span>

* [<span data-ttu-id="1205b-186">Úvod do gRPC na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1205b-186">Introduction to gRPC on ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [<span data-ttu-id="1205b-187">Služby gRPC s jazykem C#</span><span class="sxs-lookup"><span data-stu-id="1205b-187">gRPC services with C#</span></span>](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [<span data-ttu-id="1205b-188">Migrace služeb gRPC z C-core do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1205b-188">Migrating gRPC services from C-core to ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
