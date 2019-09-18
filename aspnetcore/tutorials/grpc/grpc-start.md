---
title: Vytvoření klienta a serveru .NET Core gRPC v ASP.NET Core
author: juntaoluo
description: V tomto kurzu se dozvíte, jak vytvořit službu gRPC a klienta gRPC na ASP.NET Core. Naučte se, jak vytvořit projekt služby gRPC, upravit soubor. a přidat volání duplexního streamování.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 8/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 924aa3880fc7f2aa777d4ab2e5ea3bed38e227d4
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081186"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="a8d46-104">Kurz: Vytvoření klienta a serveru gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8d46-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="a8d46-105">Od [Jan Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="a8d46-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="a8d46-106">V tomto kurzu se dozvíte, jak vytvořit klienta .NET Core [gRPC](https://grpc.io/docs/guides/) a Server služby ASP.NET Core gRPC.</span><span class="sxs-lookup"><span data-stu-id="a8d46-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="a8d46-107">Na konci budete mít klienta gRPC, který komunikuje se službou Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="a8d46-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="a8d46-108">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a8d46-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="a8d46-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="a8d46-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a8d46-110">Vytvořte server gRPC.</span><span class="sxs-lookup"><span data-stu-id="a8d46-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="a8d46-111">Vytvořte klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="a8d46-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="a8d46-112">Otestujte službu gRPC Client pomocí služby gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="a8d46-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a8d46-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a8d46-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8d46-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8d46-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8d46-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8d46-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8d46-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a8d46-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="a8d46-117">Vytvoření služby gRPC</span><span class="sxs-lookup"><span data-stu-id="a8d46-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8d46-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8d46-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a8d46-119">Spusťte aplikaci Visual Studio a vyberte možnost **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="a8d46-120">Případně můžete v nabídce **soubor** sady Visual Studio vybrat možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a8d46-121">V dialogovém okně **vytvořit nový projekt** vyberte **Služba GRPC** a vyberte **Další**:</span><span class="sxs-lookup"><span data-stu-id="a8d46-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![\* \* Vytvořit nový projekt \* \* dialog](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="a8d46-123">Pojmenujte projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="a8d46-124">Je důležité pojmenovat projekt *GrpcGreeter* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="a8d46-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="a8d46-125">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-125">Select **Create**.</span></span>
* <span data-ttu-id="a8d46-126">V dialogovém okně **vytvořit novou službu gRPC** :</span><span class="sxs-lookup"><span data-stu-id="a8d46-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="a8d46-127">Je vybraná Šablona **služby gRPC** .</span><span class="sxs-lookup"><span data-stu-id="a8d46-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="a8d46-128">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-128">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8d46-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8d46-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a8d46-130">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a8d46-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a8d46-131">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="a8d46-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="a8d46-132">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a8d46-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="a8d46-133">Příkaz vytvoří ve složce GrpcGreeter novou službu gRPC. `dotnet new`</span><span class="sxs-lookup"><span data-stu-id="a8d46-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="a8d46-134">Příkaz otevře složku GrpcGreeter v nové instanci Visual Studio Code. `code`</span><span class="sxs-lookup"><span data-stu-id="a8d46-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="a8d46-135">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' GrpcGreeter '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="a8d46-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="a8d46-136">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8d46-137">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a8d46-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a8d46-138">Z terminálu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a8d46-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="a8d46-139">Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="a8d46-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="a8d46-140">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="a8d46-140">Open the project</span></span>

<span data-ttu-id="a8d46-141">V aplikaci Visual Studio vyberte **soubor** > **otevřít**a potom vyberte soubor *GrpcGreeter. sln* .</span><span class="sxs-lookup"><span data-stu-id="a8d46-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.sln* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="a8d46-142">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="a8d46-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8d46-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8d46-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a8d46-144">Stisknutím `Ctrl+F5` klávesy spustíte službu gRPC bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="a8d46-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="a8d46-145">Visual Studio spouští službu z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a8d46-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8d46-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8d46-146">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a8d46-147">Spusťte projekt gRPC Greeter *GrpcGreeter* z příkazového řádku pomocí `dotnet run`příkazu.</span><span class="sxs-lookup"><span data-stu-id="a8d46-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8d46-148">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a8d46-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a8d46-149">Spusťte projekt gRPC Greeter *GrpcGreeter* z příkazového řádku pomocí `dotnet run`příkazu.</span><span class="sxs-lookup"><span data-stu-id="a8d46-149">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

---

<span data-ttu-id="a8d46-150">V protokolech se zobrazuje služba, `https://localhost:5001`na které naslouchá.</span><span class="sxs-lookup"><span data-stu-id="a8d46-150">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="a8d46-151">Šablona gRPC je nakonfigurovaná tak, aby používala [protokol TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="a8d46-151">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="a8d46-152">gRPC klienti potřebují pro volání serveru použít protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a8d46-152">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="a8d46-153">macOS nepodporuje ASP.NET Core gRPC s protokolem TLS.</span><span class="sxs-lookup"><span data-stu-id="a8d46-153">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="a8d46-154">K úspěšnému spuštění gRPC služeb na macOS se vyžaduje další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a8d46-154">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="a8d46-155">Další informace najdete v tématu [nepovedlo se spustit aplikaci ASP.NET Core gRPC v MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="a8d46-155">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="a8d46-156">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="a8d46-156">Examine the project files</span></span>

<span data-ttu-id="a8d46-157">Soubory projektu *GrpcGreeter* :</span><span class="sxs-lookup"><span data-stu-id="a8d46-157">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="a8d46-158">*pozdrav.* &ndash; v tomto případě soubor `Greeter` *...* dedefinuje gRPC a slouží k vygenerování prostředků serveru gRPC.</span><span class="sxs-lookup"><span data-stu-id="a8d46-158">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="a8d46-159">Další informace najdete v tématu [Úvod do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="a8d46-159">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="a8d46-160">Složka *služeb* : Obsahuje implementaci `Greeter` služby.</span><span class="sxs-lookup"><span data-stu-id="a8d46-160">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="a8d46-161">*appSettings. JSON* &ndash; obsahuje konfigurační data, jako je protokol, který používá Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a8d46-161">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="a8d46-162">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a8d46-162">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="a8d46-163">Program.cs&ndash; obsahuje vstupní bod pro službu gRPC.</span><span class="sxs-lookup"><span data-stu-id="a8d46-163">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="a8d46-164">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="a8d46-164">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="a8d46-165">Startup.cs&ndash; obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8d46-165">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="a8d46-166">Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="a8d46-166">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="a8d46-167">Vytvoření klienta gRPC v konzolové aplikaci .NET</span><span class="sxs-lookup"><span data-stu-id="a8d46-167">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8d46-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8d46-168">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a8d46-169">Otevřete druhou instanci aplikace Visual Studio a vyberte možnost **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-169">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="a8d46-170">V dialogovém okně **vytvořit nový projekt** vyberte **Konzolová aplikace (.NET Core)** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-170">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="a8d46-171">Do textového pole **název** zadejte **GrpcGreeterClient** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-171">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8d46-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8d46-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a8d46-173">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a8d46-173">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a8d46-174">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="a8d46-174">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="a8d46-175">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a8d46-175">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8d46-176">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a8d46-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a8d46-177">Postupujte podle pokynů v tématu vytvoření [kompletního řešení .NET Core na MacOS pomocí Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="a8d46-177">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="a8d46-178">Přidat požadované balíčky</span><span class="sxs-lookup"><span data-stu-id="a8d46-178">Add required packages</span></span>

<span data-ttu-id="a8d46-179">Projekt klienta gRPC vyžaduje následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="a8d46-179">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="a8d46-180">[Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8d46-180">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="a8d46-181">[Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje rozhraní API pro zprávy C#Protobuf pro.</span><span class="sxs-lookup"><span data-stu-id="a8d46-181">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="a8d46-182">[Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/), které obsahují C# podporu nástrojů pro soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="a8d46-182">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="a8d46-183">Balíček nástrojů se za běhu nevyžaduje, takže závislost je označená jako `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="a8d46-183">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8d46-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8d46-184">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a8d46-185">Nainstalujte balíčky buď pomocí konzoly Správce balíčků (PMC), nebo spravujte balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="a8d46-185">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="a8d46-186">Možnost PMC pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="a8d46-186">PMC option to install packages</span></span>

* <span data-ttu-id="a8d46-187">V aplikaci Visual Studio vyberte **nástroje** >  > **Správce balíčků NuGet** **Konzola správce balíčků** .</span><span class="sxs-lookup"><span data-stu-id="a8d46-187">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="a8d46-188">V okně **konzoly Správce balíčků** spusťte příkaz `cd GrpcGreeterClient` , aby se změnily adresáře do složky obsahující soubory *GrpcGreeterClient. csproj* .</span><span class="sxs-lookup"><span data-stu-id="a8d46-188">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="a8d46-189">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a8d46-189">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client -prerelease
  Install-Package Google.Protobuf -prerelease
  Install-Package Grpc.Tools -prerelease
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="a8d46-190">Správa možností balíčků NuGet pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="a8d46-190">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="a8d46-191">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** > **Spravovat balíčky NuGet** .</span><span class="sxs-lookup"><span data-stu-id="a8d46-191">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="a8d46-192">Vyberte kartu **Procházet** .</span><span class="sxs-lookup"><span data-stu-id="a8d46-192">Select the **Browse** tab.</span></span>
* <span data-ttu-id="a8d46-193">Do vyhledávacího pole zadejte **Grpc .NET. Client** .</span><span class="sxs-lookup"><span data-stu-id="a8d46-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="a8d46-194">Na kartě **Procházet** vyberte balíček **Grpc .NET. Client** a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-194">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="a8d46-195">Opakujte pro `Google.Protobuf` a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="a8d46-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8d46-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8d46-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a8d46-197">Z **integrovaného terminálu**spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a8d46-197">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8d46-198">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a8d46-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a8d46-199">Klikněte pravým tlačítkem na složku **balíčky** v **oblast řešení** > **Přidat balíčky** .</span><span class="sxs-lookup"><span data-stu-id="a8d46-199">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="a8d46-200">Do vyhledávacího pole zadejte **Grpc .NET. Client** .</span><span class="sxs-lookup"><span data-stu-id="a8d46-200">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="a8d46-201">V podokně výsledků vyberte balíček **Grpc .NET. Client** a vyberte **Přidat balíček** .</span><span class="sxs-lookup"><span data-stu-id="a8d46-201">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="a8d46-202">Opakujte pro `Google.Protobuf` a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="a8d46-202">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="a8d46-203">Přidat pozdrav. proto</span><span class="sxs-lookup"><span data-stu-id="a8d46-203">Add greet.proto</span></span>

* <span data-ttu-id="a8d46-204">V klientském projektu gRPC *vytvořte složku.*</span><span class="sxs-lookup"><span data-stu-id="a8d46-204">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="a8d46-205">Zkopírujte soubor *Protos\greet.proto* ze služby gRPC Greeter do projektu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="a8d46-205">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="a8d46-206">Upravte soubor projektu *GrpcGreeterClient. csproj* :</span><span class="sxs-lookup"><span data-stu-id="a8d46-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8d46-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8d46-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="a8d46-208">Klikněte pravým tlačítkem na projekt a vyberte **Upravit soubor projektu**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8d46-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8d46-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="a8d46-210">Vyberte soubor *GrpcGreeterClient. csproj* .</span><span class="sxs-lookup"><span data-stu-id="a8d46-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8d46-211">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a8d46-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="a8d46-212">Klikněte pravým tlačítkem na projekt a vyberte **nástroje** > **Upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="a8d46-212">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="a8d46-213">Přidejte skupinu položek s `<Protobuf>` prvkem, který odkazuje na soubor *Greeting.* dekáže:</span><span class="sxs-lookup"><span data-stu-id="a8d46-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="a8d46-214">Vytvoření klienta Greeter</span><span class="sxs-lookup"><span data-stu-id="a8d46-214">Create the Greeter client</span></span>

<span data-ttu-id="a8d46-215">Sestavte projekt pro vytvoření typů v `GrpcGreeter` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="a8d46-215">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="a8d46-216">`GrpcGreeter` Typy jsou generovány automaticky procesem sestavení.</span><span class="sxs-lookup"><span data-stu-id="a8d46-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="a8d46-217">Aktualizujte soubor *program.cs* klienta gRPC pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="a8d46-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="a8d46-218">*Program.cs* obsahuje vstupní bod a logiku pro klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="a8d46-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="a8d46-219">Klient s pozdravem vytvořil:</span><span class="sxs-lookup"><span data-stu-id="a8d46-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="a8d46-220">Vytvoří se instance obsahující informace pro vytvoření připojení ke službě gRPC. `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="a8d46-220">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="a8d46-221">`HttpClient` Použití k vytvoření kanálu gRPC a klienta Greeter:</span><span class="sxs-lookup"><span data-stu-id="a8d46-221">Using the `HttpClient` to construct a gRPC channel and the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="a8d46-222">Klient Greeter volá asynchronní `SayHello` metodu.</span><span class="sxs-lookup"><span data-stu-id="a8d46-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="a8d46-223">Zobrazí se výsledek `SayHello` volání:</span><span class="sxs-lookup"><span data-stu-id="a8d46-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="a8d46-224">Test klienta gRPC pomocí služby gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="a8d46-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8d46-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8d46-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a8d46-226">Ve službě Greeter stiskněte klávesu `Ctrl+F5` a spusťte server bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="a8d46-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="a8d46-227">V projektu stisknutím klávesy `Ctrl+F5` spusťte klienta bez ladicího programu. `GrpcGreeterClient`</span><span class="sxs-lookup"><span data-stu-id="a8d46-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8d46-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8d46-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a8d46-229">Spusťte službu Greeter.</span><span class="sxs-lookup"><span data-stu-id="a8d46-229">Start the Greeter service.</span></span>
* <span data-ttu-id="a8d46-230">Spusťte klienta.</span><span class="sxs-lookup"><span data-stu-id="a8d46-230">Start the client.</span></span>


# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8d46-231">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a8d46-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a8d46-232">Spusťte službu Greeter.</span><span class="sxs-lookup"><span data-stu-id="a8d46-232">Start the Greeter service.</span></span>
* <span data-ttu-id="a8d46-233">Spusťte klienta.</span><span class="sxs-lookup"><span data-stu-id="a8d46-233">Start the client.</span></span>

---

<span data-ttu-id="a8d46-234">Klient pošle službě pozdrav zprávy s názvem, který obsahuje, *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="a8d46-234">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="a8d46-235">Služba odešle jako odpověď zprávu "Hello GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="a8d46-235">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="a8d46-236">Odpověď "Hello GreeterClient" se zobrazí na příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="a8d46-236">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="a8d46-237">Služba gRPC zaznamenává podrobnosti o úspěšném volání do protokolů zapsaných do příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="a8d46-237">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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

> [!NOTE]
> <span data-ttu-id="a8d46-238">Kód v tomto článku vyžaduje k zabezpečení služby gRPC ASP.NET Core certifikát pro vývoj HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a8d46-238">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="a8d46-239">Pokud se klient u zprávy `The remote certificate is invalid according to the validation procedure.`nezdařil, vývojový certifikát není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="a8d46-239">If the client fails with the message `The remote certificate is invalid according to the validation procedure.`, the development certificate is not trusted.</span></span> <span data-ttu-id="a8d46-240">Pokyny k vyřešení tohoto problému naleznete v tématu [Trust ASP.NET Core certifikát pro vývoj https ve Windows a MacOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="a8d46-240">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

### <a name="next-steps"></a><span data-ttu-id="a8d46-241">Další kroky</span><span class="sxs-lookup"><span data-stu-id="a8d46-241">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
