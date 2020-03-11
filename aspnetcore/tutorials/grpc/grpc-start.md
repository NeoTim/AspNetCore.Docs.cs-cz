---
title: Vytvoření klienta a serveru .NET Core gRPC v ASP.NET Core
author: juntaoluo
description: V tomto kurzu se dozvíte, jak vytvořit službu gRPC a klienta gRPC na ASP.NET Core. Naučte se, jak vytvořit projekt služby gRPC, upravit soubor. a přidat volání duplexního streamování.
ms.author: johluo
ms.date: 12/05/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 0cedeb021427455c3f60a8a8cc36b52794a055bc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665821"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="66092-104">Kurz: Vytvoření klienta a serveru gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66092-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="66092-105">Od [Jan Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="66092-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="66092-106">V tomto kurzu se dozvíte, jak vytvořit klienta .NET Core [gRPC](https://grpc.io/docs/guides/) a Server služby ASP.NET Core gRPC.</span><span class="sxs-lookup"><span data-stu-id="66092-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="66092-107">Na konci budete mít klienta gRPC, který komunikuje se službou Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="66092-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="66092-108">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="66092-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="66092-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="66092-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="66092-110">Vytvořte server gRPC.</span><span class="sxs-lookup"><span data-stu-id="66092-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="66092-111">Vytvořte klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="66092-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="66092-112">Otestujte službu gRPC Client pomocí služby gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="66092-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="66092-113">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="66092-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66092-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66092-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="66092-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66092-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66092-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="66092-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="66092-117">Vytvoření služby gRPC</span><span class="sxs-lookup"><span data-stu-id="66092-117">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66092-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66092-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="66092-119">Spusťte aplikaci Visual Studio a vyberte možnost **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="66092-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="66092-120">Případně můžete v nabídce **soubor** sady Visual Studio vybrat **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="66092-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="66092-121">V dialogovém okně **vytvořit nový projekt** vyberte **Služba GRPC** a vyberte **Další**:</span><span class="sxs-lookup"><span data-stu-id="66092-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Vytvořit nový projekt – dialogové okno](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="66092-123">Pojmenujte projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="66092-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="66092-124">Je důležité pojmenovat projekt *GrpcGreeter* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="66092-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="66092-125">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="66092-125">Select **Create**.</span></span>
* <span data-ttu-id="66092-126">V dialogovém okně **vytvořit novou službu gRPC** :</span><span class="sxs-lookup"><span data-stu-id="66092-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="66092-127">Je vybraná Šablona **služby gRPC** .</span><span class="sxs-lookup"><span data-stu-id="66092-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="66092-128">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="66092-128">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66092-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66092-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="66092-130">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="66092-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="66092-131">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="66092-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="66092-132">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="66092-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="66092-133">Příkaz `dotnet new` vytvoří ve složce *GrpcGreeter* novou službu gRPC.</span><span class="sxs-lookup"><span data-stu-id="66092-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="66092-134">Příkaz `code` otevře složku *GrpcGreeter* v nové instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="66092-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="66092-135">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' GrpcGreeter '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="66092-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="66092-136">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="66092-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66092-137">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="66092-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="66092-138">Z terminálu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="66092-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="66092-139">Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="66092-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="66092-140">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="66092-140">Open the project</span></span>

<span data-ttu-id="66092-141">V aplikaci Visual Studio vyberte **soubor** > **otevřít**a potom vyberte soubor *GrpcGreeter. csproj* .</span><span class="sxs-lookup"><span data-stu-id="66092-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="66092-142">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="66092-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="66092-143">Protokoly zobrazují službu, která naslouchá na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="66092-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="66092-144">Šablona gRPC je nakonfigurovaná tak, aby používala [protokol TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="66092-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="66092-145">gRPC klienti potřebují pro volání serveru použít protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="66092-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="66092-146">macOS nepodporuje ASP.NET Core gRPC s protokolem TLS.</span><span class="sxs-lookup"><span data-stu-id="66092-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="66092-147">K úspěšnému spuštění gRPC služeb na macOS se vyžaduje další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="66092-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="66092-148">Další informace najdete v tématu [nepovedlo se spustit aplikaci ASP.NET Core gRPC v MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="66092-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="66092-149">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="66092-149">Examine the project files</span></span>

<span data-ttu-id="66092-150">Soubory projektu *GrpcGreeter* :</span><span class="sxs-lookup"><span data-stu-id="66092-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="66092-151">*pozdrav.* v tomto případě &ndash; soubor *......* "definuje `Greeter` gRPC a slouží ke generování prostředků serveru gRPC.</span><span class="sxs-lookup"><span data-stu-id="66092-151">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="66092-152">Další informace najdete v tématu [Úvod do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="66092-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="66092-153">Složka *Services* : obsahuje implementaci služby `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="66092-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="66092-154">*appSettings. json* &ndash; obsahuje konfigurační data, jako je například protokol, který používá Kestrel.</span><span class="sxs-lookup"><span data-stu-id="66092-154">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="66092-155">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="66092-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="66092-156">*Program.cs* &ndash; obsahuje vstupní bod pro službu gRPC.</span><span class="sxs-lookup"><span data-stu-id="66092-156">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="66092-157">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="66092-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="66092-158">*Startup.cs* &ndash; obsahuje kód, který nakonfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="66092-158">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="66092-159">Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="66092-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="66092-160">Vytvoření klienta gRPC v konzolové aplikaci .NET</span><span class="sxs-lookup"><span data-stu-id="66092-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66092-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66092-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="66092-162">Otevřete druhou instanci aplikace Visual Studio a vyberte možnost **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="66092-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="66092-163">V dialogovém okně **vytvořit nový projekt** vyberte **Konzolová aplikace (.NET Core)** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="66092-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="66092-164">Do textového pole **název** zadejte **GrpcGreeterClient** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="66092-164">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66092-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66092-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="66092-166">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="66092-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="66092-167">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="66092-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="66092-168">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="66092-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66092-169">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="66092-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="66092-170">Postupujte podle pokynů v tématu vytvoření [kompletního řešení .NET Core na MacOS pomocí Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="66092-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="66092-171">Přidat požadované balíčky</span><span class="sxs-lookup"><span data-stu-id="66092-171">Add required packages</span></span>

<span data-ttu-id="66092-172">Projekt klienta gRPC vyžaduje následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="66092-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="66092-173">[Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="66092-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="66092-174">[Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje rozhraní API pro zprávy C#Protobuf pro.</span><span class="sxs-lookup"><span data-stu-id="66092-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="66092-175">[Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/), které obsahují C# podporu nástrojů pro soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="66092-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="66092-176">Balíček nástrojů se za běhu nevyžaduje, takže je tato závislost označená jako `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="66092-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66092-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66092-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66092-178">Nainstalujte balíčky buď pomocí konzoly Správce balíčků (PMC), nebo spravujte balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="66092-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="66092-179">Možnost PMC pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="66092-179">PMC option to install packages</span></span>

* <span data-ttu-id="66092-180">V aplikaci Visual Studio vyberte **nástroje** > **správce balíčků NuGet** > **Konzola správce balíčků** .</span><span class="sxs-lookup"><span data-stu-id="66092-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="66092-181">V okně **konzoly Správce balíčků** spusťte `cd GrpcGreeterClient` pro změnu adresáře do složky, která obsahuje soubory *GrpcGreeterClient. csproj* .</span><span class="sxs-lookup"><span data-stu-id="66092-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="66092-182">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="66092-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="66092-183">Správa možností balíčků NuGet pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="66092-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="66092-184">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** > **Spravovat balíčky NuGet** .</span><span class="sxs-lookup"><span data-stu-id="66092-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="66092-185">Vyberte kartu **Procházet**.</span><span class="sxs-lookup"><span data-stu-id="66092-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="66092-186">Do vyhledávacího pole zadejte **Grpc .NET. Client** .</span><span class="sxs-lookup"><span data-stu-id="66092-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="66092-187">Na kartě **Procházet** vyberte balíček **Grpc .NET. Client** a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="66092-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="66092-188">Opakujte pro `Google.Protobuf` a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="66092-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66092-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66092-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="66092-190">Z **integrovaného terminálu**spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="66092-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66092-191">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="66092-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="66092-192">Klikněte pravým tlačítkem na složku **balíčky** v **oblast řešení** > **Přidat balíčky** .</span><span class="sxs-lookup"><span data-stu-id="66092-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="66092-193">Do vyhledávacího pole zadejte **Grpc .NET. Client** .</span><span class="sxs-lookup"><span data-stu-id="66092-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="66092-194">V podokně výsledků vyberte balíček **Grpc .NET. Client** a vyberte **Přidat balíček** .</span><span class="sxs-lookup"><span data-stu-id="66092-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="66092-195">Opakujte pro `Google.Protobuf` a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="66092-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="66092-196">Přidat pozdrav. proto</span><span class="sxs-lookup"><span data-stu-id="66092-196">Add greet.proto</span></span>

* <span data-ttu-id="66092-197">V klientském projektu gRPC *vytvořte složku.*</span><span class="sxs-lookup"><span data-stu-id="66092-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="66092-198">Zkopírujte soubor *Protos\greet.proto* ze služby gRPC Greeter do projektu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="66092-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="66092-199">Upravte soubor projektu *GrpcGreeterClient. csproj* :</span><span class="sxs-lookup"><span data-stu-id="66092-199">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="66092-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66092-200">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="66092-201">Klikněte pravým tlačítkem na projekt a vyberte **Upravit soubor projektu**.</span><span class="sxs-lookup"><span data-stu-id="66092-201">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="66092-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66092-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="66092-203">Vyberte soubor *GrpcGreeterClient. csproj* .</span><span class="sxs-lookup"><span data-stu-id="66092-203">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66092-204">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="66092-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="66092-205">Klikněte pravým tlačítkem na projekt a vyberte **nástroje** > **Upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="66092-205">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="66092-206">Přidejte skupinu položek s `<Protobuf>` prvkem, který odkazuje na soubor *Greeting.* dekáže:</span><span class="sxs-lookup"><span data-stu-id="66092-206">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="66092-207">Vytvoření klienta Greeter</span><span class="sxs-lookup"><span data-stu-id="66092-207">Create the Greeter client</span></span>

<span data-ttu-id="66092-208">Sestavte projekt pro vytvoření typů v oboru názvů `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="66092-208">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="66092-209">Typy `GrpcGreeter` jsou automaticky generovány procesem sestavení.</span><span class="sxs-lookup"><span data-stu-id="66092-209">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="66092-210">Aktualizujte soubor *program.cs* klienta gRPC pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="66092-210">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="66092-211">*Program.cs* obsahuje vstupní bod a logiku pro klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="66092-211">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="66092-212">Klient s pozdravem vytvořil:</span><span class="sxs-lookup"><span data-stu-id="66092-212">The Greeter client is created by:</span></span>

* <span data-ttu-id="66092-213">Vytvoření instance `GrpcChannel` obsahující informace pro vytvoření připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="66092-213">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="66092-214">Použití `GrpcChannel` k vytvoření klienta Greeter:</span><span class="sxs-lookup"><span data-stu-id="66092-214">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="66092-215">Klient Greeter volá asynchronní metodu `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="66092-215">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="66092-216">Zobrazí se výsledek volání `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="66092-216">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="66092-217">Test klienta gRPC pomocí služby gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="66092-217">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66092-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66092-218">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="66092-219">V případě služby Greeter stiskněte `Ctrl+F5` a spusťte server bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="66092-219">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="66092-220">V projektu `GrpcGreeterClient` stisknutím `Ctrl+F5` spusťte klienta bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="66092-220">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66092-221">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66092-221">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="66092-222">Spusťte službu Greeter.</span><span class="sxs-lookup"><span data-stu-id="66092-222">Start the Greeter service.</span></span>
* <span data-ttu-id="66092-223">Spusťte klienta.</span><span class="sxs-lookup"><span data-stu-id="66092-223">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66092-224">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="66092-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="66092-225">Spusťte službu Greeter.</span><span class="sxs-lookup"><span data-stu-id="66092-225">Start the Greeter service.</span></span>
* <span data-ttu-id="66092-226">Spusťte klienta.</span><span class="sxs-lookup"><span data-stu-id="66092-226">Start the client.</span></span>

---

<span data-ttu-id="66092-227">Klient pošle službě pozdrav zprávy s názvem, který obsahuje, *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="66092-227">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="66092-228">Služba odešle jako odpověď zprávu "Hello GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="66092-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="66092-229">Odpověď "Hello GreeterClient" se zobrazí na příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="66092-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="66092-230">Služba gRPC zaznamenává podrobnosti o úspěšném volání do protokolů zapsaných do příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="66092-230">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="66092-231">Kód v tomto článku vyžaduje k zabezpečení služby gRPC ASP.NET Core certifikát pro vývoj HTTPS.</span><span class="sxs-lookup"><span data-stu-id="66092-231">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="66092-232">Pokud se klient nezdařil s `The remote certificate is invalid according to the validation procedure.`zprávy, vývojový certifikát není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="66092-232">If the client fails with the message `The remote certificate is invalid according to the validation procedure.`, the development certificate is not trusted.</span></span> <span data-ttu-id="66092-233">Pokyny k vyřešení tohoto problému naleznete v tématu [Trust ASP.NET Core certifikát pro vývoj https ve Windows a MacOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="66092-233">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="66092-234">Další kroky</span><span class="sxs-lookup"><span data-stu-id="66092-234">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
