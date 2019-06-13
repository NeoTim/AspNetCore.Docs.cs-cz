---
title: Vytvoření .NET Core gRPC klientem a serverem v ASP.NET Core
author: juntaoluo
description: Tento kurz ukazuje, jak vytvořit klienta služby a gRPC gRPC v ASP.NET Core. Zjistěte, jak vytvořit projekt gRPC služby, upravte soubor proto a přidat duplexní streamování volání.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 919db3f31310342657c89100a6e25e8293648a9f
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034803"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="39f03-104">Kurz: Vytvoření gRPC klientem a serverem v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39f03-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="39f03-105">Podle [Luo Jan](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="39f03-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="39f03-106">Tento kurz ukazuje, jak vytvořit .NET Core [gRPC](https://grpc.io/docs/guides/) klienta a ASP.NET Core gRPC serveru.</span><span class="sxs-lookup"><span data-stu-id="39f03-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="39f03-107">Na konci budete mít gRPC klienta, který komunikuje s gRPC Greeter služby.</span><span class="sxs-lookup"><span data-stu-id="39f03-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="39f03-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="39f03-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="39f03-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="39f03-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="39f03-110">Vytvoření gRPC serveru.</span><span class="sxs-lookup"><span data-stu-id="39f03-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="39f03-111">Vytvoření klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="39f03-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="39f03-112">Testovat službu klienta gRPC s gRPC Greeter služby.</span><span class="sxs-lookup"><span data-stu-id="39f03-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="39f03-113">Vytvoření služby gRPC</span><span class="sxs-lookup"><span data-stu-id="39f03-113">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="39f03-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39f03-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39f03-115">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="39f03-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="39f03-116">V **vytvořte nový projekt** dialogového okna, vyberte **webové aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="39f03-116">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="39f03-117">Vyberte **další**</span><span class="sxs-lookup"><span data-stu-id="39f03-117">Select **Next**</span></span>
* <span data-ttu-id="39f03-118">Pojmenujte projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="39f03-118">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="39f03-119">Je důležité projekt pojmenujte *GrpcGreeter* tak obory názvů budou porovnávány názvy při zkopírujte a vložte kód.</span><span class="sxs-lookup"><span data-stu-id="39f03-119">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="39f03-120">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="39f03-120">Select **Create**</span></span>
* <span data-ttu-id="39f03-121">V **vytvořit novou webovou aplikaci ASP.NET Core** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="39f03-121">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="39f03-122">Vyberte **.NET Core** a **ASP.NET Core 3.0** v rozevíracích nabídek.</span><span class="sxs-lookup"><span data-stu-id="39f03-122">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="39f03-123">Vyberte **gRPC služby** šablony.</span><span class="sxs-lookup"><span data-stu-id="39f03-123">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="39f03-124">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="39f03-124">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="39f03-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39f03-125">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="39f03-126">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="39f03-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="39f03-127">Změňte adresář (`cd`) do složky, která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="39f03-127">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="39f03-128">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="39f03-128">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="39f03-129">`dotnet new` Příkaz vytvoří novou službu gRPC u *GrpcGreeter* složky.</span><span class="sxs-lookup"><span data-stu-id="39f03-129">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="39f03-130">`code` Příkaz otevře *GrpcGreeter* složky v nové instanci sady Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="39f03-130">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="39f03-131">Zobrazí se dialogové okno s **'GrpcGreeter' chybí požadované prostředky pro sestavení a ladění. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="39f03-131">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="39f03-132">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="39f03-132">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="39f03-133">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="39f03-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="39f03-134">Z terminálu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="39f03-134">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="39f03-135">Předchozí příkazy použití [rozhraní příkazového řádku .NET Core](/dotnet/core/tools/dotnet) vytvoření gRPC služby.</span><span class="sxs-lookup"><span data-stu-id="39f03-135">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="39f03-136">Otevřete projekt</span><span class="sxs-lookup"><span data-stu-id="39f03-136">Open the project</span></span>

<span data-ttu-id="39f03-137">Ze sady Visual Studio, vyberte **soubor > Otevřít**a pak vyberte *GrpcGreeter.sln* souboru.</span><span class="sxs-lookup"><span data-stu-id="39f03-137">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="39f03-138">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="39f03-138">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="39f03-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39f03-139">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39f03-140">Stisknutím klávesy `Ctrl+F5` ke spuštění služby gRPC bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="39f03-140">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="39f03-141">Visual Studio spustí službu v příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="39f03-141">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="39f03-142">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="39f03-142">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="39f03-143">Spusťte projekt Greeter gRPC *GrpcGreeter* z příkazového řádku pomocí `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="39f03-143">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="39f03-144">V protokolech zobrazuje služba naslouchá na `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="39f03-144">The logs show the service listening on `http://localhost:50051`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="39f03-145">Zkontrolujte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="39f03-145">Examine the project files</span></span>

<span data-ttu-id="39f03-146">*GrpcGreeter* soubory projektu:</span><span class="sxs-lookup"><span data-stu-id="39f03-146">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="39f03-147">*greet.proto*: *Protos/greet.proto* soubor definuje `Greeter` gRPC a slouží ke generování gRPC serverovými prostředky.</span><span class="sxs-lookup"><span data-stu-id="39f03-147">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="39f03-148">Další informace najdete v tématu [Úvod do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="39f03-148">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="39f03-149">*Služby* složky: Obsahuje implementaci `Greeter` služby.</span><span class="sxs-lookup"><span data-stu-id="39f03-149">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="39f03-150">*appSettings.json*: Obsahuje konfigurační data, třeba protokol používá Kestrel.</span><span class="sxs-lookup"><span data-stu-id="39f03-150">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="39f03-151">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="39f03-151">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="39f03-152">*Program.cs*: Vstupní bod pro službu gRPC obsahuje.</span><span class="sxs-lookup"><span data-stu-id="39f03-152">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="39f03-153">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="39f03-153">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="39f03-154">*Startup.cs*: Obsahuje kód, který nakonfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="39f03-154">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="39f03-155">Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="39f03-155">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="39f03-156">Vytvoření klienta gRPC v konzolové aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="39f03-156">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="39f03-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39f03-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39f03-158">Vyberte **souboru** > **nový** > **projektu** z řádku nabídek.</span><span class="sxs-lookup"><span data-stu-id="39f03-158">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="39f03-159">V **vytvořte nový projekt** dialogového okna, vyberte **Konzolová aplikace (.NET Core)** .</span><span class="sxs-lookup"><span data-stu-id="39f03-159">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="39f03-160">Vyberte **další**</span><span class="sxs-lookup"><span data-stu-id="39f03-160">Select **Next**</span></span>
* <span data-ttu-id="39f03-161">V **název** textové pole, zadejte "GrpcGreeterClient".</span><span class="sxs-lookup"><span data-stu-id="39f03-161">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="39f03-162">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="39f03-162">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="39f03-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39f03-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="39f03-164">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="39f03-164">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="39f03-165">Změňte adresář (`cd`) do složky, která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="39f03-165">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="39f03-166">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="39f03-166">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="39f03-167">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="39f03-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="39f03-168">Postupujte podle pokynů [tady](/dotnet/core/tutorials/using-on-mac-vs-full-solution) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="39f03-168">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="39f03-169">Přidání požadovaných balíčků</span><span class="sxs-lookup"><span data-stu-id="39f03-169">Add required packages</span></span>

<span data-ttu-id="39f03-170">Přidejte do projektu klienta gRPC následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="39f03-170">Add the following packages to the gRPC client project:</span></span>

* <span data-ttu-id="39f03-171">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="39f03-171">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="39f03-172">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje protobuf zpráva rozhraní API pro C#.</span><span class="sxs-lookup"><span data-stu-id="39f03-172">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="39f03-173">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), který obsahuje C# Podpora nástrojů pro soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="39f03-173">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="39f03-174">Balíček nástroje není nutné za běhu, takže závislost je označená pomocí `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="39f03-174">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="39f03-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39f03-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39f03-176">Instalace balíčků pomocí konzoly Správce balíčků (PMC) nebo spravovat balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="39f03-176">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="39f03-177">Možnost PMC se mají balíčky nainstalovat</span><span class="sxs-lookup"><span data-stu-id="39f03-177">PMC option to install packages</span></span>

* <span data-ttu-id="39f03-178">Ze sady Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**</span><span class="sxs-lookup"><span data-stu-id="39f03-178">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="39f03-179">Z **Konzola správce balíčků** okno, přejděte do adresáře, ve kterém *GrpcGreeterClient.csproj* soubor existuje.</span><span class="sxs-lookup"><span data-stu-id="39f03-179">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="39f03-180">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="39f03-180">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="39f03-181">Spravovat balíčky NuGet možnost instalace balíčků</span><span class="sxs-lookup"><span data-stu-id="39f03-181">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="39f03-182">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**</span><span class="sxs-lookup"><span data-stu-id="39f03-182">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="39f03-183">Vyberte **Procházet** kartu.</span><span class="sxs-lookup"><span data-stu-id="39f03-183">Select the **Browse** tab.</span></span>
* <span data-ttu-id="39f03-184">Zadejte **Grpc.Core** do vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="39f03-184">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="39f03-185">Vyberte **Grpc.Core** balíček ze **Procházet** kartě a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="39f03-185">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="39f03-186">Opakujte pro `Google.Protobuf` a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="39f03-186">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="39f03-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39f03-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="39f03-188">Spusťte následující příkazy z **integrovaný terminál**:</span><span class="sxs-lookup"><span data-stu-id="39f03-188">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="39f03-189">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="39f03-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="39f03-190">Klikněte pravým tlačítkem myši **balíčky** složky v **oblasti řešení** > **přidat balíčky**</span><span class="sxs-lookup"><span data-stu-id="39f03-190">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="39f03-191">Zadejte **Grpc.Net.Client** do vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="39f03-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="39f03-192">Vyberte **Grpc.Net.Client** balíček v podokně výsledků a vyberte **přidat balíček**</span><span class="sxs-lookup"><span data-stu-id="39f03-192">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="39f03-193">Opakujte pro `Google.Protobuf` a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="39f03-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="39f03-194">Přidat greet.proto</span><span class="sxs-lookup"><span data-stu-id="39f03-194">Add greet.proto</span></span>

* <span data-ttu-id="39f03-195">Vytvoření **Protos** složky v klientském projektu gRPC.</span><span class="sxs-lookup"><span data-stu-id="39f03-195">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="39f03-196">Kopírovat **Protos\greet.proto** souboru z gRPC Greeter služby gRPC klientský projekt.</span><span class="sxs-lookup"><span data-stu-id="39f03-196">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="39f03-197">Upravit *GrpcGreeterClient.csproj* souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="39f03-197">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="39f03-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39f03-198">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="39f03-199">Klikněte pravým tlačítkem na projekt a vyberte **upravit GrpcGreeterClient.csproj**.</span><span class="sxs-lookup"><span data-stu-id="39f03-199">Right-click the project and select the **Edit GrpcGreeterClient.csproj**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="39f03-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39f03-200">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="39f03-201">Vyberte *GrpcGreeterClient.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="39f03-201">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="39f03-202">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="39f03-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="39f03-203">Klikněte pravým tlačítkem na projekt a vyberte **nástroje > Upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="39f03-203">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="39f03-204">Přidat **greet.proto** do souboru `<Protobuf>` skupiny položek GrpcGreeterClient souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="39f03-204">Add the **greet.proto** file to the `<Protobuf>` item group of the GrpcGreeterClient project file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

<span data-ttu-id="39f03-205">Sestavení projektu klienta k aktivaci generování C# prostředky klienta.</span><span class="sxs-lookup"><span data-stu-id="39f03-205">Build the client project to trigger the generation of the C# client assets.</span></span>

### <a name="create-the-greeter-client"></a><span data-ttu-id="39f03-206">Vytvoření klienta Greeter</span><span class="sxs-lookup"><span data-stu-id="39f03-206">Create the Greeter client</span></span>

<span data-ttu-id="39f03-207">Projekt pro vytvoření typů v sestavení **Greeter** oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="39f03-207">Build the project to create the types in the **Greeter** namespace.</span></span> <span data-ttu-id="39f03-208">`Greeter` Typy jsou automaticky generovány procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="39f03-208">The `Greeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="39f03-209">Také aktualizovat klienta sady gRPC *Program.cs* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="39f03-209">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="39f03-210">*Soubor program.cs* obsahuje vstupní bod a logiku pro gRPC klienta.</span><span class="sxs-lookup"><span data-stu-id="39f03-210">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="39f03-211">Klient Greeter vytvořil:</span><span class="sxs-lookup"><span data-stu-id="39f03-211">The Greeter client is created by:</span></span>

* <span data-ttu-id="39f03-212">Vytvoření instance `HttpClient` obsahující informace o vytvoření připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="39f03-212">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="39f03-213">Použití `HttpClient` k sestavení kompletních Greeter klienta:</span><span class="sxs-lookup"><span data-stu-id="39f03-213">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-9)]

<span data-ttu-id="39f03-214">Klient Greeter volá asynchronní `SayHello` metody.</span><span class="sxs-lookup"><span data-stu-id="39f03-214">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="39f03-215">Výsledkem `SayHello` volání se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="39f03-215">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=10-12)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="39f03-216">Testovací klient gRPC s gRPC Greeter služby</span><span class="sxs-lookup"><span data-stu-id="39f03-216">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="39f03-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39f03-217">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39f03-218">Ve službě Greeter stiskněte `Ctrl+F5` spuštění serveru bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="39f03-218">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="39f03-219">V `GrpcGreeterClient` projekt, stiskněte klávesu `Ctrl+F5` spuštění serveru bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="39f03-219">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="39f03-220">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="39f03-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="39f03-221">Spusťte službu Greeter.</span><span class="sxs-lookup"><span data-stu-id="39f03-221">Start the Greeter service.</span></span>
* <span data-ttu-id="39f03-222">Spusťte klienta.</span><span class="sxs-lookup"><span data-stu-id="39f03-222">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="39f03-223">Klient odešle pozdrav ke službě a zobrazí se zpráva obsahující jeho jméno "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="39f03-223">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="39f03-224">Služba odesílá zprávu "Hello GreeterClient" jako odpověď.</span><span class="sxs-lookup"><span data-stu-id="39f03-224">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="39f03-225">Na příkazovém řádku se zobrazí odpověď "Hello GreeterClient":</span><span class="sxs-lookup"><span data-stu-id="39f03-225">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="39f03-226">Služba gRPC zaznamenával podrobnosti o úspěšném volání v protokolech zapsána do příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="39f03-226">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="next-steps"></a><span data-ttu-id="39f03-227">Další kroky</span><span class="sxs-lookup"><span data-stu-id="39f03-227">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
