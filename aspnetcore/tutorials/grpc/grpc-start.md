---
title: Vytvoření klienta a serveru .NET Core gRPC v ASP.NET Core
author: juntaoluo
description: V tomto kurzu se dozvíte, jak vytvořit službu gRPC a klienta gRPC na ASP.NET Core. Naučte se, jak vytvořit projekt služby gRPC, upravit soubor. a přidat volání duplexního streamování.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 3e90e3b17186757fe157fb6641888786bb7a0df2
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412519"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="97ac8-104">Kurz: Vytvoření klienta a serveru gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="97ac8-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="97ac8-105">Od [Jan Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="97ac8-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="97ac8-106">V tomto kurzu se dozvíte, jak vytvořit klienta .NET Core [gRPC](https://grpc.io/docs/guides/) a Server služby ASP.NET Core gRPC.</span><span class="sxs-lookup"><span data-stu-id="97ac8-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="97ac8-107">Na konci budete mít klienta gRPC, který komunikuje se službou Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="97ac8-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="97ac8-108">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="97ac8-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="97ac8-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="97ac8-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="97ac8-110">Vytvořte server gRPC.</span><span class="sxs-lookup"><span data-stu-id="97ac8-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="97ac8-111">Vytvořte klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="97ac8-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="97ac8-112">Otestujte službu gRPC Client pomocí služby gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="97ac8-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="97ac8-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="97ac8-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97ac8-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97ac8-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97ac8-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97ac8-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97ac8-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="97ac8-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="97ac8-117">Vytvoření služby gRPC</span><span class="sxs-lookup"><span data-stu-id="97ac8-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97ac8-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97ac8-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="97ac8-119">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="97ac8-119">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="97ac8-120">V dialogovém okně **vytvořit nový projekt** vyberte **ASP.NET Core webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="97ac8-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="97ac8-121">Vybrat **Další**</span><span class="sxs-lookup"><span data-stu-id="97ac8-121">Select **Next**</span></span>
* <span data-ttu-id="97ac8-122">Pojmenujte projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="97ac8-122">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="97ac8-123">Je důležité pojmenovat projekt *GrpcGreeter* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="97ac8-123">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="97ac8-124">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="97ac8-124">Select **Create**</span></span>
* <span data-ttu-id="97ac8-125">V dialogovém okně **vytvořit novou ASP.NET Core webovou aplikaci** :</span><span class="sxs-lookup"><span data-stu-id="97ac8-125">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="97ac8-126">V rozevíracích nabídkách vyberte **.NET Core** a **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="97ac8-126">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="97ac8-127">Vyberte šablonu **služby gRPC** .</span><span class="sxs-lookup"><span data-stu-id="97ac8-127">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="97ac8-128">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="97ac8-128">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97ac8-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97ac8-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="97ac8-130">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="97ac8-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="97ac8-131">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="97ac8-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="97ac8-132">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="97ac8-132">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="97ac8-133">Příkaz vytvoří ve složce GrpcGreeter novou službu gRPC.  `dotnet new`</span><span class="sxs-lookup"><span data-stu-id="97ac8-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="97ac8-134">Příkaz otevře složku GrpcGreeter v nové instanci Visual Studio Code.  `code`</span><span class="sxs-lookup"><span data-stu-id="97ac8-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="97ac8-135">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' GrpcGreeter '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="97ac8-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="97ac8-136">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="97ac8-136">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97ac8-137">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="97ac8-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="97ac8-138">Z terminálu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="97ac8-138">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="97ac8-139">Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="97ac8-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="97ac8-140">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="97ac8-140">Open the project</span></span>

<span data-ttu-id="97ac8-141">V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *GrpcGreeter. sln* .</span><span class="sxs-lookup"><span data-stu-id="97ac8-141">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="97ac8-142">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="97ac8-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97ac8-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97ac8-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="97ac8-144">Stisknutím `Ctrl+F5` klávesy spustíte službu gRPC bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="97ac8-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="97ac8-145">Visual Studio spouští službu z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="97ac8-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="97ac8-146">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="97ac8-146">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="97ac8-147">Spusťte projekt gRPC Greeter *GrpcGreeter* z příkazového řádku pomocí `dotnet run`příkazu.</span><span class="sxs-lookup"><span data-stu-id="97ac8-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="97ac8-148">V protokolech se zobrazuje služba, `https://localhost:5001`na které naslouchá.</span><span class="sxs-lookup"><span data-stu-id="97ac8-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="97ac8-149">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="97ac8-149">Examine the project files</span></span>

<span data-ttu-id="97ac8-150">Soubory projektu *GrpcGreeter* :</span><span class="sxs-lookup"><span data-stu-id="97ac8-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="97ac8-151">*greet.proto*: Soubor *....* dedefinuje `Greeter` gRPC a slouží k vygenerování prostředků serveru gRPC.</span><span class="sxs-lookup"><span data-stu-id="97ac8-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="97ac8-152">Další informace najdete v tématu [Úvod do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="97ac8-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="97ac8-153">Složka *služeb* : Obsahuje implementaci `Greeter` služby.</span><span class="sxs-lookup"><span data-stu-id="97ac8-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="97ac8-154">*appSettings.json*: Obsahuje konfigurační data, jako je protokol, který používá Kestrel.</span><span class="sxs-lookup"><span data-stu-id="97ac8-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="97ac8-155">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="97ac8-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="97ac8-156">*Program.cs*: Obsahuje vstupní bod pro službu gRPC.</span><span class="sxs-lookup"><span data-stu-id="97ac8-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="97ac8-157">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="97ac8-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="97ac8-158">*Startup.cs*: Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="97ac8-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="97ac8-159">Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="97ac8-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="97ac8-160">Vytvoření klienta gRPC v konzolové aplikaci .NET</span><span class="sxs-lookup"><span data-stu-id="97ac8-160">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97ac8-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97ac8-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="97ac8-162">Otevřete druhou instanci aplikace Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="97ac8-162">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="97ac8-163">Z řádku nabídek vyberte **soubor** > **Nový** > **projekt** .</span><span class="sxs-lookup"><span data-stu-id="97ac8-163">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="97ac8-164">V dialogovém okně **vytvořit nový projekt** vyberte konzolová **aplikace (.NET Core)** .</span><span class="sxs-lookup"><span data-stu-id="97ac8-164">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="97ac8-165">Vybrat **Další**</span><span class="sxs-lookup"><span data-stu-id="97ac8-165">Select **Next**</span></span>
* <span data-ttu-id="97ac8-166">Do textového pole **název** zadejte "GrpcGreeterClient".</span><span class="sxs-lookup"><span data-stu-id="97ac8-166">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="97ac8-167">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="97ac8-167">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97ac8-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97ac8-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="97ac8-169">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="97ac8-169">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="97ac8-170">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="97ac8-170">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="97ac8-171">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="97ac8-171">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97ac8-172">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="97ac8-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="97ac8-173">Podle pokynů [zde](/dotnet/core/tutorials/using-on-mac-vs-full-solution) můžete vytvořit konzolovou aplikaci s názvem *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="97ac8-173">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="97ac8-174">Přidat požadované balíčky</span><span class="sxs-lookup"><span data-stu-id="97ac8-174">Add required packages</span></span>

<span data-ttu-id="97ac8-175">Projekt klienta gRPC vyžaduje následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="97ac8-175">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="97ac8-176">[Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="97ac8-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="97ac8-177">[Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje rozhraní API pro zprávy C#Protobuf pro.</span><span class="sxs-lookup"><span data-stu-id="97ac8-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="97ac8-178">[Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/), které obsahují C# podporu nástrojů pro soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="97ac8-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="97ac8-179">Balíček nástrojů se za běhu nevyžaduje, takže závislost je označená jako `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="97ac8-179">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97ac8-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97ac8-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="97ac8-181">Nainstalujte balíčky buď pomocí konzoly Správce balíčků (PMC), nebo spravujte balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="97ac8-181">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="97ac8-182">Možnost PMC pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="97ac8-182">PMC option to install packages</span></span>

* <span data-ttu-id="97ac8-183">V aplikaci Visual Studio vyberte **nástroje** >  > **Správce balíčků NuGet** **Konzola správce balíčků** .</span><span class="sxs-lookup"><span data-stu-id="97ac8-183">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="97ac8-184">V okně **konzoly Správce balíčků** přejděte do adresáře, ve kterém existuje soubor *GrpcGreeterClient. csproj* .</span><span class="sxs-lookup"><span data-stu-id="97ac8-184">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="97ac8-185">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="97ac8-185">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="97ac8-186">Správa možností balíčků NuGet pro instalaci balíčků</span><span class="sxs-lookup"><span data-stu-id="97ac8-186">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="97ac8-187">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** > **Spravovat balíčky NuGet** .</span><span class="sxs-lookup"><span data-stu-id="97ac8-187">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="97ac8-188">Vyberte kartu **Procházet** .</span><span class="sxs-lookup"><span data-stu-id="97ac8-188">Select the **Browse** tab.</span></span>
* <span data-ttu-id="97ac8-189">Do vyhledávacího pole zadejte **Grpc. Core** .</span><span class="sxs-lookup"><span data-stu-id="97ac8-189">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="97ac8-190">Na kartě **Procházet** vyberte balíček **Grpc. Core** a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="97ac8-190">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="97ac8-191">Opakujte pro `Google.Protobuf` a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="97ac8-191">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97ac8-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97ac8-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="97ac8-193">Z **integrovaného terminálu**spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="97ac8-193">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97ac8-194">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="97ac8-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="97ac8-195">Klikněte pravým tlačítkem na složku **balíčky** v **oblast řešení** > **Přidat balíčky** .</span><span class="sxs-lookup"><span data-stu-id="97ac8-195">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="97ac8-196">Do vyhledávacího pole zadejte **Grpc .NET. Client** .</span><span class="sxs-lookup"><span data-stu-id="97ac8-196">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="97ac8-197">V podokně výsledků vyberte balíček **Grpc .NET. Client** a vyberte **Přidat balíček** .</span><span class="sxs-lookup"><span data-stu-id="97ac8-197">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="97ac8-198">Opakujte pro `Google.Protobuf` a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="97ac8-198">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="97ac8-199">Přidat pozdrav. proto</span><span class="sxs-lookup"><span data-stu-id="97ac8-199">Add greet.proto</span></span>

* <span data-ttu-id="97ac8-200">V klientském projektu gRPC **vytvořte složku.**</span><span class="sxs-lookup"><span data-stu-id="97ac8-200">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="97ac8-201">Zkopírujte soubor **Protos\greet.proto** ze služby gRPC Greeter do projektu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="97ac8-201">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="97ac8-202">Upravte soubor projektu *GrpcGreeterClient. csproj* :</span><span class="sxs-lookup"><span data-stu-id="97ac8-202">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97ac8-203">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97ac8-203">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="97ac8-204">Klikněte pravým tlačítkem na projekt a vyberte **Upravit soubor projektu**.</span><span class="sxs-lookup"><span data-stu-id="97ac8-204">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97ac8-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97ac8-205">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="97ac8-206">Vyberte soubor *GrpcGreeterClient. csproj* .</span><span class="sxs-lookup"><span data-stu-id="97ac8-206">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97ac8-207">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="97ac8-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="97ac8-208">Klikněte pravým tlačítkem na projekt a vyberte **nástroje > upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="97ac8-208">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="97ac8-209">Přidejte skupinu položek s `<Protobuf>` prvkem, který odkazuje na soubor **Greeting.** dekáže:</span><span class="sxs-lookup"><span data-stu-id="97ac8-209">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="97ac8-210">Vytvoření klienta Greeter</span><span class="sxs-lookup"><span data-stu-id="97ac8-210">Create the Greeter client</span></span>

<span data-ttu-id="97ac8-211">Sestavte projekt pro vytvoření typů v `GrpcGreeter` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="97ac8-211">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="97ac8-212">`GrpcGreeter` Typy jsou generovány automaticky procesem sestavení.</span><span class="sxs-lookup"><span data-stu-id="97ac8-212">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="97ac8-213">Aktualizujte soubor *program.cs* klienta gRPC pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="97ac8-213">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="97ac8-214">*Program.cs* obsahuje vstupní bod a logiku pro klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="97ac8-214">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="97ac8-215">Klient s pozdravem vytvořil:</span><span class="sxs-lookup"><span data-stu-id="97ac8-215">The Greeter client is created by:</span></span>

* <span data-ttu-id="97ac8-216">Vytvoří se instance obsahující informace pro vytvoření připojení ke službě gRPC. `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="97ac8-216">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="97ac8-217">`HttpClient` Použití ke konstrukci klienta Greeter:</span><span class="sxs-lookup"><span data-stu-id="97ac8-217">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="97ac8-218">Klient Greeter volá asynchronní `SayHello` metodu.</span><span class="sxs-lookup"><span data-stu-id="97ac8-218">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="97ac8-219">Zobrazí se výsledek `SayHello` volání:</span><span class="sxs-lookup"><span data-stu-id="97ac8-219">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="97ac8-220">Test klienta gRPC pomocí služby gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="97ac8-220">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97ac8-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97ac8-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="97ac8-222">Ve službě Greeter stiskněte klávesu `Ctrl+F5` a spusťte server bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="97ac8-222">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="97ac8-223">V projektu stisknutím klávesy `Ctrl+F5` spusťte server bez ladicího programu. `GrpcGreeterClient`</span><span class="sxs-lookup"><span data-stu-id="97ac8-223">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="97ac8-224">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="97ac8-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="97ac8-225">Spusťte službu Greeter.</span><span class="sxs-lookup"><span data-stu-id="97ac8-225">Start the Greeter service.</span></span>
* <span data-ttu-id="97ac8-226">Spusťte klienta.</span><span class="sxs-lookup"><span data-stu-id="97ac8-226">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="97ac8-227">Klient pošle službě pozdravu zprávu, která obsahuje název "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="97ac8-227">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="97ac8-228">Služba odešle jako odpověď zprávu "Hello GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="97ac8-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="97ac8-229">Odpověď "Hello GreeterClient" se zobrazí na příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="97ac8-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="97ac8-230">Služba gRPC zaznamenává podrobnosti o úspěšném volání do protokolů zapsaných do příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="97ac8-230">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="next-steps"></a><span data-ttu-id="97ac8-231">Další postup</span><span class="sxs-lookup"><span data-stu-id="97ac8-231">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
