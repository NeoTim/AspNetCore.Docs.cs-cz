---
title: 'Kurz: Začínáme s gRPC v ASP.NET Core'
author: juntaoluo
description: Tato série kurzů ukazuje, jak vytvořit gRPC služba v ASP.NET Core. Zjistěte, jak vytvořit projekt služby gRPC, upravte soubor proto a přidat duplexní streamování volání.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 2/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: a67050331cc8563b1baf5312b92910c1bbdfbd69
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672564"
---
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="173ba-104">Kurz: Začínáme se službou gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="173ba-104">Tutorial: Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="173ba-105">Podle [Luo Jan](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="173ba-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="173ba-106">V tomto kurzu se naučíte se základy vytváření gRPC služba v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="173ba-106">This tutorial teaches the basics of building a gRPC service on ASP.NET Core.</span></span>

<span data-ttu-id="173ba-107">Na konci budete mít gRPC služba, která vypisuje greetings.</span><span class="sxs-lookup"><span data-stu-id="173ba-107">At the end, you'll have a gRPC service that echoes greetings.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

<span data-ttu-id="173ba-108">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="173ba-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="173ba-109">Vytvoření služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="173ba-109">Create a gRPC service.</span></span>
> * <span data-ttu-id="173ba-110">GRPC službu spusťte.</span><span class="sxs-lookup"><span data-stu-id="173ba-110">Run the gRPC service.</span></span>
> * <span data-ttu-id="173ba-111">Zkontrolujte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="173ba-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="173ba-112">Vytvoření služby gRPC</span><span class="sxs-lookup"><span data-stu-id="173ba-112">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="173ba-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="173ba-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="173ba-114">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="173ba-114">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="173ba-115">Vytvořte novou webovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="173ba-115">Create a new ASP.NET Core Web Application.</span></span>
  <span data-ttu-id="173ba-116">![Nová webová aplikace ASP.NET Core](grpc-start/_static/np_3_0.1.png)</span><span class="sxs-lookup"><span data-stu-id="173ba-116">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.1.png)</span></span>
* <span data-ttu-id="173ba-117">Pojmenujte projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="173ba-117">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="173ba-118">Je důležité projekt pojmenujte *GrpcGreeter* tak obory názvů budou porovnávány názvy při zkopírujte a vložte kód.</span><span class="sxs-lookup"><span data-stu-id="173ba-118">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="173ba-119">![Nová webová aplikace ASP.NET Core](grpc-start/_static/np_3_0.2.png)</span><span class="sxs-lookup"><span data-stu-id="173ba-119">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.2.png)</span></span>
* <span data-ttu-id="173ba-120">Vyberte **.NET Core** a **ASP.NET Core 3.0** v rozevírací nabídce.</span><span class="sxs-lookup"><span data-stu-id="173ba-120">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown.</span></span> <span data-ttu-id="173ba-121">Zvolte **gRPC služby** šablony.</span><span class="sxs-lookup"><span data-stu-id="173ba-121">Choose the **gRPC Service** template.</span></span>

  <span data-ttu-id="173ba-122">Následující počáteční projekt je vytvořen:</span><span class="sxs-lookup"><span data-stu-id="173ba-122">The following starter project is created:</span></span>

  ![Průzkumník řešení](grpc-start/_static/se3.0.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="173ba-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="173ba-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="173ba-125">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="173ba-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="173ba-126">Změňte adresář (`cd`) do složky, která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="173ba-126">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="173ba-127">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="173ba-127">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="173ba-128">`dotnet new` Příkaz vytvoří novou službu gRPC u *GrpcGreeter* složky.</span><span class="sxs-lookup"><span data-stu-id="173ba-128">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="173ba-129">`code` Příkaz otevře *GrpcGreeter* složky v nové instanci sady Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="173ba-129">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="173ba-130">Zobrazí se dialogové okno s **'GrpcGreeter' chybí požadované prostředky pro sestavení a ladění. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="173ba-130">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="173ba-131">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="173ba-131">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="173ba-132">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="173ba-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="173ba-133">Z terminálu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="173ba-133">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="173ba-134">Předchozí příkazy použití [rozhraní příkazového řádku .NET Core](/dotnet/core/tools/dotnet) vytvoření gRPC služby.</span><span class="sxs-lookup"><span data-stu-id="173ba-134">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="173ba-135">Otevřete projekt</span><span class="sxs-lookup"><span data-stu-id="173ba-135">Open the project</span></span>

<span data-ttu-id="173ba-136">Ze sady Visual Studio, vyberte **soubor > Otevřít**a pak vyberte *GrpcGreeter.sln* souboru.</span><span class="sxs-lookup"><span data-stu-id="173ba-136">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="173ba-137">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="173ba-137">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="173ba-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="173ba-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="173ba-139">Stisknutím kláves Ctrl + F5 ke spuštění služby gRPC bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="173ba-139">Press Ctrl+F5 to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="173ba-140">Visual Studio spustí službu v příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="173ba-140">Visual Studio runs the service in a command prompt.</span></span> <span data-ttu-id="173ba-141">V protokolech zobrazuje, že služba spustilo se naslouchání v `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="173ba-141">The logs show that the service started listening on `http://localhost:50051`.</span></span>

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/server_start.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="173ba-143">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="173ba-143">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="173ba-144">Pomocí příkazového řádku, spusťte projekt Greeter gRPC GrpcGreeter `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="173ba-144">Run the gRPC Greeter project GrpcGreeter from the command line using `dotnet run`.</span></span> <span data-ttu-id="173ba-145">V protokolech zobrazuje, že služba spustilo se naslouchání v `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="173ba-145">The logs show that the service started listening on `http://localhost:50051`.</span></span>

```console
dbug: Grpc.AspNetCore.Server.Internal.GrpcServiceBinder[1]
      Added gRPC method 'SayHello' to service 'Greet.Greeter'. Method type: 'Unary', route pattern: '/Greet.Greeter/SayHello'.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\Docs\aspnetcore\tutorials\grpc\grpc-start\samples\GrpcGreeter
```

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="173ba-146">Další kurz vám ukáže jak sestavit gRPC klienta, který je nutný k otestování služby Greeter.</span><span class="sxs-lookup"><span data-stu-id="173ba-146">The next tutorial will demonstrate how to build a gRPC client, which is required to test the Greeter service.</span></span>

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="173ba-147">Zkontrolujte soubory projektu gRPC projektu</span><span class="sxs-lookup"><span data-stu-id="173ba-147">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="173ba-148">GrpcGreeter soubory:</span><span class="sxs-lookup"><span data-stu-id="173ba-148">GrpcGreeter files:</span></span>

* <span data-ttu-id="173ba-149">greet.proto: *Protos/greet.proto* soubor definuje `Greeter` gRPC a slouží ke generování gRPC serverovými prostředky.</span><span class="sxs-lookup"><span data-stu-id="173ba-149">greet.proto: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="173ba-150">Další informace naleznete v tématu <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="173ba-150">For more information, see <xref:grpc/index>.</span></span>
* <span data-ttu-id="173ba-151">*Služby* složky: Obsahuje implementaci `Greeter` služby.</span><span class="sxs-lookup"><span data-stu-id="173ba-151">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="173ba-152">*appSettings.json*: obsahuje konfigurační data, třeba protokol používá Kestrel.</span><span class="sxs-lookup"><span data-stu-id="173ba-152">*appSettings.json*:Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="173ba-153">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="173ba-153">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="173ba-154">*Program.cs*: Vstupní bod pro službu gRPC obsahuje.</span><span class="sxs-lookup"><span data-stu-id="173ba-154">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="173ba-155">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="173ba-155">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="173ba-156">Startup.cs: Obsahuje kód, který nakonfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="173ba-156">Startup.cs: Contains code that configures app behavior.</span></span> <span data-ttu-id="173ba-157">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="173ba-157">For more information, see <xref:fundamentals/startup>.</span></span>

### <a name="test-the-service"></a><span data-ttu-id="173ba-158">Testování služby</span><span class="sxs-lookup"><span data-stu-id="173ba-158">Test the service</span></span>

## <a name="additional-resources"></a><span data-ttu-id="173ba-159">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="173ba-159">Additional resources</span></span>

<span data-ttu-id="173ba-160">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="173ba-160">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="173ba-161">GRPC službu vytvořili.</span><span class="sxs-lookup"><span data-stu-id="173ba-161">Created a gRPC service.</span></span>
> * <span data-ttu-id="173ba-162">Spuštění služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="173ba-162">Ran the gRPC service.</span></span>
> * <span data-ttu-id="173ba-163">Prozkoumat soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="173ba-163">Examined the project files.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="173ba-164">Další: Vytvoření klienta gRPC .NET Core</span><span class="sxs-lookup"><span data-stu-id="173ba-164">Next: Create a .NET Core gRPC client</span></span>](xref:tutorials/grpc/grpc-client)