---
title: 'Kurz: Začínáme s gRPC v ASP.NET Core'
author: juntaoluo
description: Tato série kurzů ukazuje, jak vytvořit gRPC služba v ASP.NET Core. Zjistěte, jak vytvořit projekt služby gRPC, upravte soubor proto a přidat duplexní streamování volání.
ms.author: johluo
ms.date: 2/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 793d13d0fbac4f2f46b7cd8490ded294e597ca9d
ms.sourcegitcommit: a467828b5e4eaae291d961ffe2279a571900de23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58142597"
---
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="7fc88-104">Kurz: Začínáme se službou gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7fc88-104">Tutorial: Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="7fc88-105">Podle [Luo Jan](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="7fc88-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="7fc88-106">V tomto kurzu se naučíte se základy vytváření gRPC služba v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7fc88-106">This tutorial teaches the basics of building a gRPC service on ASP.NET Core.</span></span>

<span data-ttu-id="7fc88-107">Na konci budete mít gRPC služba, která vypisuje greetings.</span><span class="sxs-lookup"><span data-stu-id="7fc88-107">At the end, you'll have a gRPC service that echoes greetings.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

<span data-ttu-id="7fc88-108">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="7fc88-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7fc88-109">Vytvoření služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="7fc88-109">Create a gRPC service.</span></span>
> * <span data-ttu-id="7fc88-110">Spuštění služby.</span><span class="sxs-lookup"><span data-stu-id="7fc88-110">Run the service.</span></span>
> * <span data-ttu-id="7fc88-111">Zkontrolujte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="7fc88-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="7fc88-112">Vytvoření služby gRPC</span><span class="sxs-lookup"><span data-stu-id="7fc88-112">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7fc88-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7fc88-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7fc88-114">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="7fc88-114">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7fc88-115">Vytvořte novou webovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7fc88-115">Create a new ASP.NET Core Web Application.</span></span>
  <span data-ttu-id="7fc88-116">![Nová webová aplikace ASP.NET Core](grpc-start/_static/np_3_0.1.png)</span><span class="sxs-lookup"><span data-stu-id="7fc88-116">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.1.png)</span></span>
* <span data-ttu-id="7fc88-117">Pojmenujte projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="7fc88-117">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="7fc88-118">Je důležité projekt pojmenujte *GrpcGreeter* tak obory názvů budou porovnávány názvy při zkopírujte a vložte kód.</span><span class="sxs-lookup"><span data-stu-id="7fc88-118">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="7fc88-119">![Nová webová aplikace ASP.NET Core](grpc-start/_static/np_3_0.2.png)</span><span class="sxs-lookup"><span data-stu-id="7fc88-119">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.2.png)</span></span>
* <span data-ttu-id="7fc88-120">Vyberte **.NET Core** a **ASP.NET Core 3.0** v rozevírací nabídce.</span><span class="sxs-lookup"><span data-stu-id="7fc88-120">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown.</span></span> <span data-ttu-id="7fc88-121">Zvolte **gRPC služby** šablony.</span><span class="sxs-lookup"><span data-stu-id="7fc88-121">Choose the **gRPC Service** template.</span></span>

  <span data-ttu-id="7fc88-122">Následující počáteční projekt je vytvořen:</span><span class="sxs-lookup"><span data-stu-id="7fc88-122">The following starter project is created:</span></span>

  ![Průzkumník řešení](grpc-start/_static/se3.0.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7fc88-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7fc88-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7fc88-125">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="7fc88-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7fc88-126">Změňte adresář (`cd`) do složky, která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="7fc88-126">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="7fc88-127">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="7fc88-127">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="7fc88-128">`dotnet new` Příkaz vytvoří novou službu gRPC u *GrpcGreeter* složky.</span><span class="sxs-lookup"><span data-stu-id="7fc88-128">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="7fc88-129">`code` Příkaz otevře *GrpcGreeter* složky v nové instanci sady Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7fc88-129">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="7fc88-130">Zobrazí se dialogové okno s **'GrpcGreeter' chybí požadované prostředky pro sestavení a ladění. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="7fc88-130">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="7fc88-131">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="7fc88-131">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7fc88-132">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7fc88-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7fc88-133">Z terminálu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="7fc88-133">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="7fc88-134">Předchozí příkazy použití [rozhraní příkazového řádku .NET Core](/dotnet/core/tools/dotnet) vytvoření gRPC služby.</span><span class="sxs-lookup"><span data-stu-id="7fc88-134">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="7fc88-135">Otevřete projekt</span><span class="sxs-lookup"><span data-stu-id="7fc88-135">Open the project</span></span>

<span data-ttu-id="7fc88-136">Ze sady Visual Studio, vyberte **soubor > Otevřít**a pak vyberte *GrpcGreeter.sln* souboru.</span><span class="sxs-lookup"><span data-stu-id="7fc88-136">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="test-the-service"></a><span data-ttu-id="7fc88-137">Testování služby</span><span class="sxs-lookup"><span data-stu-id="7fc88-137">Test the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7fc88-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7fc88-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7fc88-139">Zkontrolujte, **GrpcGreeter.Server** je nastavit jako spouštěný projekt a stisknutím kláves Ctrl + F5 ke spuštění služby gRPC bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="7fc88-139">Ensure the **GrpcGreeter.Server** is set as the Startup Project and press Ctrl+F5 to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="7fc88-140">Visual Studio spustí službu v příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="7fc88-140">Visual Studio runs the service in a command prompt.</span></span> <span data-ttu-id="7fc88-141">V protokolech zobrazuje, že služba spustilo se naslouchání v `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="7fc88-141">The logs show that the service started listening on `http://localhost:50051`.</span></span>

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/server_start.png)

* <span data-ttu-id="7fc88-143">Jakmile je služba spuštěná, nastavte **GrpcGreeter.Client** je nastavit jako spouštěný projekt a stisknutím kláves Ctrl + F5 a spusťte tak klienta bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="7fc88-143">Once the service is running, set the **GrpcGreeter.Client** is set as the Startup Project and press Ctrl+F5 to run the client without the debugger.</span></span>

  <span data-ttu-id="7fc88-144">Klient odešle pozdrav ke službě a zobrazí se zpráva obsahující jeho jméno "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="7fc88-144">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="7fc88-145">Služba se poslat zprávu "Hello GreeterClient" jako odpověď, která se zobrazí v okně příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="7fc88-145">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/client.png)

  <span data-ttu-id="7fc88-147">Služba zaznamenával podrobnosti o úspěšném volání v protokolech zapsána do příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="7fc88-147">The service records the details of the successful call in the logs written to the command prompt.</span></span>

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7fc88-149">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7fc88-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7fc88-150">Pomocí příkazového řádku, spusťte projekt serveru GrpcGreeter.Server `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="7fc88-150">Run the Server project GrpcGreeter.Server from the command line using `dotnet run`.</span></span> <span data-ttu-id="7fc88-151">V protokolech zobrazuje, že služba spustilo se naslouchání v `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="7fc88-151">The logs show that the service started listening on `http://localhost:50051`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\example\GrpcGreeter\GrpcGreeter.Server
```

* <span data-ttu-id="7fc88-152">Pomocí samostatných příkazového řádku, spusťte klientský projekt GrpcGreeter.Client `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="7fc88-152">Run the Client project GrpcGreeter.Client from the separate command line using `dotnet run`.</span></span>

<span data-ttu-id="7fc88-153">Klient odešle pozdrav ke službě a zobrazí se zpráva obsahující jeho jméno "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="7fc88-153">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="7fc88-154">Služba se poslat zprávu "Hello GreeterClient" jako odpověď, která se zobrazí v okně příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="7fc88-154">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="7fc88-155">Služba zaznamenával podrobnosti o úspěšném volání v protokolech zapsána do příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="7fc88-155">The service records the details of the successful call in the logs written to the command prompt.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\tp\GrpcGreeter\GrpcGreeter.Server
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 107.46730000000001ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="7fc88-156">Zkontrolujte soubory projektu gRPC projektu</span><span class="sxs-lookup"><span data-stu-id="7fc88-156">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="7fc88-157">GrpcGreeter.Server soubory:</span><span class="sxs-lookup"><span data-stu-id="7fc88-157">GrpcGreeter.Server files:</span></span>

* <span data-ttu-id="7fc88-158">greet.proto: *Protos/greet.proto* soubor definuje `Greeter` gRPC a slouží ke generování gRPC serverovými prostředky.</span><span class="sxs-lookup"><span data-stu-id="7fc88-158">greet.proto: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="7fc88-159">Další informace naleznete v tématu <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="7fc88-159">For more information, see <xref:grpc/index>.</span></span>
* <span data-ttu-id="7fc88-160">*Služby* složky: Obsahuje implementaci `Greeter` služby.</span><span class="sxs-lookup"><span data-stu-id="7fc88-160">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="7fc88-161">*appSettings.json*: obsahuje konfigurační data, třeba protokol používá Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7fc88-161">*appSettings.json*:Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="7fc88-162">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="7fc88-162">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="7fc88-163">*Program.cs*: Vstupní bod pro službu gRPC obsahuje.</span><span class="sxs-lookup"><span data-stu-id="7fc88-163">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="7fc88-164">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="7fc88-164">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="7fc88-165">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="7fc88-165">Startup.cs</span></span>

<span data-ttu-id="7fc88-166">Obsahuje kód, který nakonfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="7fc88-166">Contains code that configures app behavior.</span></span> <span data-ttu-id="7fc88-167">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="7fc88-167">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="7fc88-168">soubor GrpcGreeter.Client gRPC klienta:</span><span class="sxs-lookup"><span data-stu-id="7fc88-168">gRPC client GrpcGreeter.Client file:</span></span>

<span data-ttu-id="7fc88-169">*Soubor program.cs* obsahuje vstupní bod a logiku pro gRPC klienta.</span><span class="sxs-lookup"><span data-stu-id="7fc88-169">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="7fc88-170">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="7fc88-170">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7fc88-171">GRPC službu vytvořili.</span><span class="sxs-lookup"><span data-stu-id="7fc88-171">Created a gRPC service.</span></span>
> * <span data-ttu-id="7fc88-172">Spustil službu a klienta k otestování služby.</span><span class="sxs-lookup"><span data-stu-id="7fc88-172">Ran the service and a client to test the service.</span></span>
> * <span data-ttu-id="7fc88-173">Prozkoumat soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="7fc88-173">Examined the project files.</span></span>
