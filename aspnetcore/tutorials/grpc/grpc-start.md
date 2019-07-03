---
title: Vytvoření .NET Core gRPC klientem a serverem v ASP.NET Core
author: juntaoluo
description: Tento kurz ukazuje, jak vytvořit klienta služby a gRPC gRPC v ASP.NET Core. Zjistěte, jak vytvořit projekt gRPC služby, upravte soubor proto a přidat duplexní streamování volání.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 8507c3dcefeb61a4dd34a1ff967c082d287cf646
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555889"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="ccb55-104">Kurz: Vytvoření gRPC klientem a serverem v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ccb55-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="ccb55-105">Podle [Luo Jan](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="ccb55-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="ccb55-106">Tento kurz ukazuje, jak vytvořit .NET Core [gRPC](https://grpc.io/docs/guides/) klienta a ASP.NET Core gRPC serveru.</span><span class="sxs-lookup"><span data-stu-id="ccb55-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="ccb55-107">Na konci budete mít gRPC klienta, který komunikuje s gRPC Greeter služby.</span><span class="sxs-lookup"><span data-stu-id="ccb55-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="ccb55-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ccb55-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ccb55-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="ccb55-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ccb55-110">Vytvoření gRPC serveru.</span><span class="sxs-lookup"><span data-stu-id="ccb55-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="ccb55-111">Vytvoření klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="ccb55-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="ccb55-112">Testovat službu klienta gRPC s gRPC Greeter služby.</span><span class="sxs-lookup"><span data-stu-id="ccb55-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ccb55-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ccb55-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ccb55-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ccb55-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ccb55-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ccb55-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ccb55-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ccb55-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="ccb55-117">Vytvoření služby gRPC</span><span class="sxs-lookup"><span data-stu-id="ccb55-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ccb55-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ccb55-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ccb55-119">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="ccb55-119">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ccb55-120">V **vytvořte nový projekt** dialogového okna, vyberte **webové aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="ccb55-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="ccb55-121">Vyberte **další**</span><span class="sxs-lookup"><span data-stu-id="ccb55-121">Select **Next**</span></span>
* <span data-ttu-id="ccb55-122">Pojmenujte projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="ccb55-122">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="ccb55-123">Je důležité projekt pojmenujte *GrpcGreeter* tak obory názvů budou porovnávány názvy při zkopírujte a vložte kód.</span><span class="sxs-lookup"><span data-stu-id="ccb55-123">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="ccb55-124">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ccb55-124">Select **Create**</span></span>
* <span data-ttu-id="ccb55-125">V **vytvořit novou webovou aplikaci ASP.NET Core** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="ccb55-125">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="ccb55-126">Vyberte **.NET Core** a **ASP.NET Core 3.0** v rozevíracích nabídek.</span><span class="sxs-lookup"><span data-stu-id="ccb55-126">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="ccb55-127">Vyberte **gRPC služby** šablony.</span><span class="sxs-lookup"><span data-stu-id="ccb55-127">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="ccb55-128">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ccb55-128">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ccb55-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ccb55-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ccb55-130">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ccb55-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ccb55-131">Změňte adresář (`cd`) do složky, která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="ccb55-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ccb55-132">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="ccb55-132">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="ccb55-133">`dotnet new` Příkaz vytvoří novou službu gRPC u *GrpcGreeter* složky.</span><span class="sxs-lookup"><span data-stu-id="ccb55-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="ccb55-134">`code` Příkaz otevře *GrpcGreeter* složky v nové instanci sady Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ccb55-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="ccb55-135">Zobrazí se dialogové okno s **'GrpcGreeter' chybí požadované prostředky pro sestavení a ladění. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="ccb55-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="ccb55-136">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="ccb55-136">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ccb55-137">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ccb55-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ccb55-138">Z terminálu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="ccb55-138">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="ccb55-139">Předchozí příkazy použití [rozhraní příkazového řádku .NET Core](/dotnet/core/tools/dotnet) vytvoření gRPC služby.</span><span class="sxs-lookup"><span data-stu-id="ccb55-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="ccb55-140">Otevřete projekt</span><span class="sxs-lookup"><span data-stu-id="ccb55-140">Open the project</span></span>

<span data-ttu-id="ccb55-141">Ze sady Visual Studio, vyberte **soubor > Otevřít**a pak vyberte *GrpcGreeter.sln* souboru.</span><span class="sxs-lookup"><span data-stu-id="ccb55-141">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="ccb55-142">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="ccb55-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ccb55-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ccb55-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ccb55-144">Stisknutím klávesy `Ctrl+F5` ke spuštění služby gRPC bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="ccb55-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="ccb55-145">Visual Studio spustí službu v příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="ccb55-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ccb55-146">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ccb55-146">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ccb55-147">Spusťte projekt Greeter gRPC *GrpcGreeter* z příkazového řádku pomocí `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ccb55-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="ccb55-148">V protokolech zobrazuje služba naslouchá na `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="ccb55-148">The logs show the service listening on `http://localhost:50051`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="ccb55-149">Zkontrolujte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="ccb55-149">Examine the project files</span></span>

<span data-ttu-id="ccb55-150">*GrpcGreeter* soubory projektu:</span><span class="sxs-lookup"><span data-stu-id="ccb55-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="ccb55-151">*greet.proto*: *Protos/greet.proto* soubor definuje `Greeter` gRPC a slouží ke generování gRPC serverovými prostředky.</span><span class="sxs-lookup"><span data-stu-id="ccb55-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="ccb55-152">Další informace najdete v tématu [Úvod do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="ccb55-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="ccb55-153">*Služby* složky: Obsahuje implementaci `Greeter` služby.</span><span class="sxs-lookup"><span data-stu-id="ccb55-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="ccb55-154">*appSettings.json*: Obsahuje konfigurační data, třeba protokol používá Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ccb55-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="ccb55-155">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ccb55-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="ccb55-156">*Program.cs*: Vstupní bod pro službu gRPC obsahuje.</span><span class="sxs-lookup"><span data-stu-id="ccb55-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="ccb55-157">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="ccb55-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="ccb55-158">*Startup.cs*: Obsahuje kód, který nakonfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="ccb55-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="ccb55-159">Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="ccb55-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="ccb55-160">Vytvoření klienta gRPC v konzolové aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="ccb55-160">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ccb55-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ccb55-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ccb55-162">Spusťte druhou instanci aplikace Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ccb55-162">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="ccb55-163">Vyberte **souboru** > **nový** > **projektu** z řádku nabídek.</span><span class="sxs-lookup"><span data-stu-id="ccb55-163">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="ccb55-164">V **vytvořte nový projekt** dialogového okna, vyberte **Konzolová aplikace (.NET Core)** .</span><span class="sxs-lookup"><span data-stu-id="ccb55-164">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="ccb55-165">Vyberte **další**</span><span class="sxs-lookup"><span data-stu-id="ccb55-165">Select **Next**</span></span>
* <span data-ttu-id="ccb55-166">V **název** textové pole, zadejte "GrpcGreeterClient".</span><span class="sxs-lookup"><span data-stu-id="ccb55-166">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="ccb55-167">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ccb55-167">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ccb55-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ccb55-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ccb55-169">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ccb55-169">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ccb55-170">Změňte adresář (`cd`) do složky, která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="ccb55-170">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ccb55-171">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="ccb55-171">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ccb55-172">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ccb55-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ccb55-173">Postupujte podle pokynů [tady](/dotnet/core/tutorials/using-on-mac-vs-full-solution) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="ccb55-173">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="ccb55-174">Přidání požadovaných balíčků</span><span class="sxs-lookup"><span data-stu-id="ccb55-174">Add required packages</span></span>

<span data-ttu-id="ccb55-175">GRPC klientský projekt vyžaduje následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="ccb55-175">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="ccb55-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ccb55-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="ccb55-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje protobuf zpráva rozhraní API pro C#.</span><span class="sxs-lookup"><span data-stu-id="ccb55-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="ccb55-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), který obsahuje C# Podpora nástrojů pro soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="ccb55-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="ccb55-179">Balíček nástroje není nutné za běhu, takže závislost je označená pomocí `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="ccb55-179">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ccb55-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ccb55-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ccb55-181">Instalace balíčků pomocí konzoly Správce balíčků (PMC) nebo spravovat balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="ccb55-181">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="ccb55-182">Možnost PMC se mají balíčky nainstalovat</span><span class="sxs-lookup"><span data-stu-id="ccb55-182">PMC option to install packages</span></span>

* <span data-ttu-id="ccb55-183">Ze sady Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**</span><span class="sxs-lookup"><span data-stu-id="ccb55-183">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="ccb55-184">Z **Konzola správce balíčků** okno, přejděte do adresáře, ve kterém *GrpcGreeterClient.csproj* soubor existuje.</span><span class="sxs-lookup"><span data-stu-id="ccb55-184">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="ccb55-185">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="ccb55-185">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="ccb55-186">Spravovat balíčky NuGet možnost instalace balíčků</span><span class="sxs-lookup"><span data-stu-id="ccb55-186">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="ccb55-187">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**</span><span class="sxs-lookup"><span data-stu-id="ccb55-187">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="ccb55-188">Vyberte **Procházet** kartu.</span><span class="sxs-lookup"><span data-stu-id="ccb55-188">Select the **Browse** tab.</span></span>
* <span data-ttu-id="ccb55-189">Zadejte **Grpc.Core** do vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="ccb55-189">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="ccb55-190">Vyberte **Grpc.Core** balíček ze **Procházet** kartě a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="ccb55-190">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="ccb55-191">Opakujte pro `Google.Protobuf` a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="ccb55-191">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ccb55-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ccb55-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ccb55-193">Spusťte následující příkazy z **integrovaný terminál**:</span><span class="sxs-lookup"><span data-stu-id="ccb55-193">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ccb55-194">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ccb55-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ccb55-195">Klikněte pravým tlačítkem myši **balíčky** složky v **oblasti řešení** > **přidat balíčky**</span><span class="sxs-lookup"><span data-stu-id="ccb55-195">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="ccb55-196">Zadejte **Grpc.Net.Client** do vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="ccb55-196">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="ccb55-197">Vyberte **Grpc.Net.Client** balíček v podokně výsledků a vyberte **přidat balíček**</span><span class="sxs-lookup"><span data-stu-id="ccb55-197">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="ccb55-198">Opakujte pro `Google.Protobuf` a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="ccb55-198">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="ccb55-199">Přidat greet.proto</span><span class="sxs-lookup"><span data-stu-id="ccb55-199">Add greet.proto</span></span>

* <span data-ttu-id="ccb55-200">Vytvoření **Protos** složky v klientském projektu gRPC.</span><span class="sxs-lookup"><span data-stu-id="ccb55-200">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="ccb55-201">Kopírovat **Protos\greet.proto** souboru z gRPC Greeter služby gRPC klientský projekt.</span><span class="sxs-lookup"><span data-stu-id="ccb55-201">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="ccb55-202">Upravit *GrpcGreeterClient.csproj* souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="ccb55-202">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ccb55-203">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ccb55-203">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="ccb55-204">Klikněte pravým tlačítkem na projekt a vyberte **upravit soubor projektu**.</span><span class="sxs-lookup"><span data-stu-id="ccb55-204">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ccb55-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ccb55-205">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="ccb55-206">Vyberte *GrpcGreeterClient.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="ccb55-206">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ccb55-207">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ccb55-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="ccb55-208">Klikněte pravým tlačítkem na projekt a vyberte **nástroje > Upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="ccb55-208">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="ccb55-209">Přidat skupiny položek `<Protobuf>` element, který odkazuje **greet.proto** souboru:</span><span class="sxs-lookup"><span data-stu-id="ccb55-209">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="ccb55-210">Vytvoření klienta Greeter</span><span class="sxs-lookup"><span data-stu-id="ccb55-210">Create the Greeter client</span></span>

<span data-ttu-id="ccb55-211">Projekt pro vytvoření typů v sestavení `GrpcGreeter` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="ccb55-211">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="ccb55-212">`GrpcGreeter` Typy jsou automaticky generovány procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="ccb55-212">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="ccb55-213">Také aktualizovat klienta sady gRPC *Program.cs* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ccb55-213">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="ccb55-214">*Soubor program.cs* obsahuje vstupní bod a logiku pro gRPC klienta.</span><span class="sxs-lookup"><span data-stu-id="ccb55-214">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="ccb55-215">Klient Greeter vytvořil:</span><span class="sxs-lookup"><span data-stu-id="ccb55-215">The Greeter client is created by:</span></span>

* <span data-ttu-id="ccb55-216">Vytvoření instance `HttpClient` obsahující informace o vytvoření připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="ccb55-216">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="ccb55-217">Použití `HttpClient` k sestavení kompletních Greeter klienta:</span><span class="sxs-lookup"><span data-stu-id="ccb55-217">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-9)]

<span data-ttu-id="ccb55-218">Klient Greeter volá asynchronní `SayHello` metody.</span><span class="sxs-lookup"><span data-stu-id="ccb55-218">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="ccb55-219">Výsledkem `SayHello` volání se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="ccb55-219">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=10-12)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="ccb55-220">Testovací klient gRPC s gRPC Greeter služby</span><span class="sxs-lookup"><span data-stu-id="ccb55-220">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ccb55-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ccb55-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ccb55-222">Ve službě Greeter stiskněte `Ctrl+F5` spuštění serveru bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="ccb55-222">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="ccb55-223">V `GrpcGreeterClient` projekt, stiskněte klávesu `Ctrl+F5` spuštění serveru bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="ccb55-223">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ccb55-224">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ccb55-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ccb55-225">Spusťte službu Greeter.</span><span class="sxs-lookup"><span data-stu-id="ccb55-225">Start the Greeter service.</span></span>
* <span data-ttu-id="ccb55-226">Spusťte klienta.</span><span class="sxs-lookup"><span data-stu-id="ccb55-226">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="ccb55-227">Klient odešle pozdrav ke službě a zobrazí se zpráva obsahující jeho jméno "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="ccb55-227">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="ccb55-228">Služba odesílá zprávu "Hello GreeterClient" jako odpověď.</span><span class="sxs-lookup"><span data-stu-id="ccb55-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="ccb55-229">Na příkazovém řádku se zobrazí odpověď "Hello GreeterClient":</span><span class="sxs-lookup"><span data-stu-id="ccb55-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="ccb55-230">Služba gRPC zaznamenával podrobnosti o úspěšném volání v protokolech zapsána do příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="ccb55-230">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="next-steps"></a><span data-ttu-id="ccb55-231">Další kroky</span><span class="sxs-lookup"><span data-stu-id="ccb55-231">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
