---
title: Vytvoření klienta a serveru .NET Core gRPC v ASP.NET Core
author: juntaoluo
description: Tento kurz ukazuje, jak vytvořit gRPC service a gRPC klienta na ASP.NET Core. Naučte se, jak vytvořit projekt služby gRPC, upravit soubor proto a přidat volání duplexního streamování.
ms.author: johluo
ms.date: 04/08/2020
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 2bbd40b4b89af170dae40b8a5277749d6bcd5faf
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994632"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="21fcf-104">Kurz: Vytvoření klienta a serveru gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21fcf-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="21fcf-105">Podle [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="21fcf-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="21fcf-106">Tento kurz ukazuje, jak vytvořit klienta [gRPC](https://grpc.io/docs/guides/) .NET Core a ASP.NET Core gRPC Server.</span><span class="sxs-lookup"><span data-stu-id="21fcf-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="21fcf-107">Na konci budete mít gRPC klienta, který komunikuje se službou gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="21fcf-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="21fcf-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="21fcf-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="21fcf-109">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="21fcf-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="21fcf-110">Vytvořte gRPC Server.</span><span class="sxs-lookup"><span data-stu-id="21fcf-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="21fcf-111">Vytvořte gRPC klienta.</span><span class="sxs-lookup"><span data-stu-id="21fcf-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="21fcf-112">Otestujte klientskou službu gRPC se službou gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="21fcf-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="21fcf-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="21fcf-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21fcf-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21fcf-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="21fcf-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21fcf-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21fcf-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="21fcf-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="21fcf-117">Vytvoření služby gRPC</span><span class="sxs-lookup"><span data-stu-id="21fcf-117">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21fcf-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21fcf-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="21fcf-119">Spusťte Visual Studio a vyberte **Vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="21fcf-120">Případně v nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="21fcf-121">V **dialogovém** okně Vytvořit nový projekt vyberte **gRPC Service** a vyberte **Další**:</span><span class="sxs-lookup"><span data-stu-id="21fcf-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Vytvořit dialogové okno vytvořit nový projekt](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="21fcf-123">Název projektu **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="21fcf-124">Je důležité pojmenovat projekt *GrpcGreeter,* aby se jmenné prostory shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="21fcf-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="21fcf-125">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-125">Select **Create**.</span></span>
* <span data-ttu-id="21fcf-126">V **dialogovém okně Vytvořit novou službu gRPC:**</span><span class="sxs-lookup"><span data-stu-id="21fcf-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="21fcf-127">Je vybrána šablona **služby gRPC.**</span><span class="sxs-lookup"><span data-stu-id="21fcf-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="21fcf-128">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-128">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="21fcf-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21fcf-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="21fcf-130">Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="21fcf-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="21fcf-131">Změňte adresáře (`cd`) na složku, která bude projekt obsahovat.</span><span class="sxs-lookup"><span data-stu-id="21fcf-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="21fcf-132">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="21fcf-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="21fcf-133">Příkaz `dotnet new` vytvoří novou službu gRPC ve složce *GrpcGreeter.*</span><span class="sxs-lookup"><span data-stu-id="21fcf-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="21fcf-134">Příkaz `code` otevře složku *GrpcGreeter* v nové instanci kódu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="21fcf-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="21fcf-135">V poli GrpcGreeter se zobrazí dialogové okno s **požadovanými prostředky k sestavení a laděním. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="21fcf-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="21fcf-136">Vyberte **ano**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21fcf-137">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="21fcf-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="21fcf-138">Z terminálu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="21fcf-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="21fcf-139">Předchozí příkazy používají [rozhraní PŘÍKAZU .NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="21fcf-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="21fcf-140">Otevření projektu</span><span class="sxs-lookup"><span data-stu-id="21fcf-140">Open the project</span></span>

<span data-ttu-id="21fcf-141">V sadě Visual Studio vyberte **Soubor** > **otevřít**a pak vyberte soubor *GrpcGreeter.csproj.*</span><span class="sxs-lookup"><span data-stu-id="21fcf-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="21fcf-142">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="21fcf-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="21fcf-143">Protokoly zobrazit službu `https://localhost:5001`naslouchání na .</span><span class="sxs-lookup"><span data-stu-id="21fcf-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="21fcf-144">Šablona gRPC je nakonfigurována pro použití [zabezpečení transportní vrstvy (TLS).](https://tools.ietf.org/html/rfc5246)</span><span class="sxs-lookup"><span data-stu-id="21fcf-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="21fcf-145">gRPC klienti musí používat PROTOKOL HTTPS pro volání serveru.</span><span class="sxs-lookup"><span data-stu-id="21fcf-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="21fcf-146">macOS nepodporuje ASP.NET Core gRPC s TLS.</span><span class="sxs-lookup"><span data-stu-id="21fcf-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="21fcf-147">K úspěšnému spuštění služeb gRPC v systému macOS je nutná další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="21fcf-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="21fcf-148">Další informace najdete [v tématu Nelze spustit ASP.NET aplikaci Core gRPC v systému macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="21fcf-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="21fcf-149">Prohlédněte si soubory projektu</span><span class="sxs-lookup"><span data-stu-id="21fcf-149">Examine the project files</span></span>

<span data-ttu-id="21fcf-150">*GrpcGreeter* soubory projektu:</span><span class="sxs-lookup"><span data-stu-id="21fcf-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="21fcf-151">*greet.proto* &ndash; Soubor *Protos/greet.proto* definuje `Greeter` gRPC a používá se ke generování prostředků serveru gRPC.</span><span class="sxs-lookup"><span data-stu-id="21fcf-151">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="21fcf-152">Další informace naleznete [v tématu Úvod do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="21fcf-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="21fcf-153">*Složka služby:* Obsahuje `Greeter` implementaci služby.</span><span class="sxs-lookup"><span data-stu-id="21fcf-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="21fcf-154">*appSettings.json* &ndash; Obsahuje konfigurační data, například protokol používaný společností Kestrel.</span><span class="sxs-lookup"><span data-stu-id="21fcf-154">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="21fcf-155">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="21fcf-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="21fcf-156">*Program.cs* &ndash; Obsahuje vstupní bod pro službu gRPC.</span><span class="sxs-lookup"><span data-stu-id="21fcf-156">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="21fcf-157">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="21fcf-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="21fcf-158">*Startup.cs* &ndash; Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="21fcf-158">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="21fcf-159">Další informace naleznete v [tématu App startup](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="21fcf-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="21fcf-160">Vytvoření klienta gRPC v aplikaci konzoly .NET</span><span class="sxs-lookup"><span data-stu-id="21fcf-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21fcf-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21fcf-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="21fcf-162">Otevřete druhou instanci sady Visual Studio a vyberte **Vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="21fcf-163">V **dialogovém** okně Vytvořit nový projekt vyberte **Console App (.NET Core)** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="21fcf-164">Do textového pole **Název** zadejte **GrpcGreeterClient** a vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-164">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="21fcf-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21fcf-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="21fcf-166">Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="21fcf-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="21fcf-167">Změňte adresáře (`cd`) na složku, která bude projekt obsahovat.</span><span class="sxs-lookup"><span data-stu-id="21fcf-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="21fcf-168">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="21fcf-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21fcf-169">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="21fcf-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="21fcf-170">Postupujte podle pokynů v [části Vytvoření kompletního řešení .NET Core v macOS pomocí Visual Studia pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) a vytvořte konzolovou aplikaci s názvem *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="21fcf-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="21fcf-171">Přidání požadovaných balíčků</span><span class="sxs-lookup"><span data-stu-id="21fcf-171">Add required packages</span></span>

<span data-ttu-id="21fcf-172">Projekt klienta gRPC vyžaduje následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="21fcf-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="21fcf-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="21fcf-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="21fcf-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje protobufové zprávy API pro C#.</span><span class="sxs-lookup"><span data-stu-id="21fcf-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="21fcf-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), který obsahuje podporu nástrojů C# pro soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="21fcf-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="21fcf-176">Balíček nástrojů není vyžadován za běhu, takže závislost je `PrivateAssets="All"`označena .</span><span class="sxs-lookup"><span data-stu-id="21fcf-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21fcf-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21fcf-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="21fcf-178">Nainstalujte balíčky pomocí konzoly Správce balíčků (PMC) nebo Spravovat balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="21fcf-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="21fcf-179">Možnost PMC pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="21fcf-179">PMC option to install packages</span></span>

* <span data-ttu-id="21fcf-180">V sadě Visual Studio vyberte **Nástroje, které** > **nástrojiNuGet Správce** > **balíčků konzoly Správce balíčků**</span><span class="sxs-lookup"><span data-stu-id="21fcf-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="21fcf-181">V okně **Konzola** správce `cd GrpcGreeterClient` balíčků spusťte a změňte adresáře do složky obsahující soubory *GrpcGreeterClient.csproj.*</span><span class="sxs-lookup"><span data-stu-id="21fcf-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="21fcf-182">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="21fcf-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="21fcf-183">Možnost Spravovat balíčky NuGet pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="21fcf-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="21fcf-184">Klikněte pravým tlačítkem myši na projekt v **Průzkumníku** > řešení**Spravovat balíčky NuGet**</span><span class="sxs-lookup"><span data-stu-id="21fcf-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="21fcf-185">Vyberte kartu **Procházet**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="21fcf-186">Do vyhledávacího pole zadejte **Grpc.Net.Client.**</span><span class="sxs-lookup"><span data-stu-id="21fcf-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="21fcf-187">Na kartě **Procházet** vyberte balíček **Grpc.Net.Client** a vyberte **Instalovat**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="21fcf-188">Opakujte `Google.Protobuf` `Grpc.Tools`pro a .</span><span class="sxs-lookup"><span data-stu-id="21fcf-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="21fcf-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21fcf-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="21fcf-190">Z **integrovaného terminálu**spusťte následující příkazy :</span><span class="sxs-lookup"><span data-stu-id="21fcf-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21fcf-191">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="21fcf-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="21fcf-192">Klikněte pravým tlačítkem myši na složku **Balíčky** v **panelu řešení** > **Přidat balíčky**</span><span class="sxs-lookup"><span data-stu-id="21fcf-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="21fcf-193">Do vyhledávacího pole zadejte **Grpc.Net.Client.**</span><span class="sxs-lookup"><span data-stu-id="21fcf-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="21fcf-194">V podokně výsledků vyberte balíček **Grpc.Net.Client** a vyberte **Přidat balíček.**</span><span class="sxs-lookup"><span data-stu-id="21fcf-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="21fcf-195">Opakujte `Google.Protobuf` `Grpc.Tools`pro a .</span><span class="sxs-lookup"><span data-stu-id="21fcf-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="21fcf-196">Přidat greet.proto</span><span class="sxs-lookup"><span data-stu-id="21fcf-196">Add greet.proto</span></span>

* <span data-ttu-id="21fcf-197">Vytvořte složku *Protos* v projektu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="21fcf-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="21fcf-198">Zkopírujte soubor *Protos\greet.proto* ze služby gRPC Greeter do klientského projektu gRPC.</span><span class="sxs-lookup"><span data-stu-id="21fcf-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="21fcf-199">Upravte soubor projektu *GrpcGreeterClient.csproj:*</span><span class="sxs-lookup"><span data-stu-id="21fcf-199">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="21fcf-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21fcf-200">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="21fcf-201">Klepněte pravým tlačítkem myši na projekt a vyberte **upravit soubor projektu**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-201">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="21fcf-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21fcf-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="21fcf-203">Vyberte soubor *GrpcGreeterClient.csproj.*</span><span class="sxs-lookup"><span data-stu-id="21fcf-203">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21fcf-204">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="21fcf-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="21fcf-205">Klepněte pravým tlačítkem myši na projekt a vyberte **příkaz Nástroje** > **upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="21fcf-205">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="21fcf-206">Přidejte skupinu `<Protobuf>` položek s prvkem, který odkazuje na soubor *greet.proto:*</span><span class="sxs-lookup"><span data-stu-id="21fcf-206">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="21fcf-207">Vytvoření klienta Greeter</span><span class="sxs-lookup"><span data-stu-id="21fcf-207">Create the Greeter client</span></span>

<span data-ttu-id="21fcf-208">Vytvořte projekt a vytvořte `GrpcGreeter` typy v oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="21fcf-208">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="21fcf-209">Typy `GrpcGreeter` jsou generovány automaticky procesem sestavení.</span><span class="sxs-lookup"><span data-stu-id="21fcf-209">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="21fcf-210">Aktualizujte soubor *klienta gRPC Program.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="21fcf-210">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="21fcf-211">*Program.cs* obsahuje vstupní bod a logiku pro klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="21fcf-211">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="21fcf-212">Klient Greeter je vytvořen:</span><span class="sxs-lookup"><span data-stu-id="21fcf-212">The Greeter client is created by:</span></span>

* <span data-ttu-id="21fcf-213">Vytvoření instance `GrpcChannel` obsahující informace pro vytvoření připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="21fcf-213">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="21fcf-214">Použití `GrpcChannel` k vytvoření klienta Greeter:</span><span class="sxs-lookup"><span data-stu-id="21fcf-214">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="21fcf-215">Klient Greeter volá asynchronní `SayHello` metodu.</span><span class="sxs-lookup"><span data-stu-id="21fcf-215">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="21fcf-216">Zobrazí se `SayHello` výsledek volání:</span><span class="sxs-lookup"><span data-stu-id="21fcf-216">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="21fcf-217">Otestujte klienta gRPC pomocí služby gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="21fcf-217">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21fcf-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21fcf-218">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="21fcf-219">Ve službě Greeter `Ctrl+F5` spusťte server bez ladicího programu stisknutím klávesy .</span><span class="sxs-lookup"><span data-stu-id="21fcf-219">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="21fcf-220">V `GrpcGreeterClient` projektu stisknutím `Ctrl+F5` klávesy spusťte klienta bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="21fcf-220">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="21fcf-221">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21fcf-221">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="21fcf-222">Spusťte službu Greeter.</span><span class="sxs-lookup"><span data-stu-id="21fcf-222">Start the Greeter service.</span></span>
* <span data-ttu-id="21fcf-223">Spusťte klienta.</span><span class="sxs-lookup"><span data-stu-id="21fcf-223">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21fcf-224">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="21fcf-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="21fcf-225">Spusťte službu Greeter.</span><span class="sxs-lookup"><span data-stu-id="21fcf-225">Start the Greeter service.</span></span>
* <span data-ttu-id="21fcf-226">Spusťte klienta.</span><span class="sxs-lookup"><span data-stu-id="21fcf-226">Start the client.</span></span>

---

<span data-ttu-id="21fcf-227">Klient odešle službu pozdrav se zprávou obsahující její název *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="21fcf-227">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="21fcf-228">Služba odešle zprávu "Hello GreeterClient" jako odpověď.</span><span class="sxs-lookup"><span data-stu-id="21fcf-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="21fcf-229">V příkazovém řádku se zobrazí odpověď "Hello GreeterClient":</span><span class="sxs-lookup"><span data-stu-id="21fcf-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="21fcf-230">Služba gRPC zaznamenává podrobnosti o úspěšném volání v protokolech zapsaných do příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="21fcf-230">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="21fcf-231">Kód v tomto článku vyžaduje ASP.NET vývojový certifikát Core HTTPS k zabezpečení služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="21fcf-231">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="21fcf-232">Pokud klient gRPC rozhraní s `The remote certificate is invalid according to the validation procedure.` chybou nezdaří se zprávou nebo `The SSL connection could not be established.`, vývojový certifikát není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="21fcf-232">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="21fcf-233">Chcete-li tento problém vyřešit, přečtěte si téma [Volání služby gRPC s nedůvěryhodným/neplatným certifikátem](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="21fcf-233">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="21fcf-234">Další kroky</span><span class="sxs-lookup"><span data-stu-id="21fcf-234">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
